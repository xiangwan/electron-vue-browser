<template>
    <div class="app">
        <chrome-tabbar ref="tabs" :tabbar="tabbar" />
        <chrome-navbar ref="navbar" :navbar="navbar" />
        <div class="content">  
         <chrome-frame :ref="'page-'+index" v-for="(bPage,index) in bPages" v-bind:index="index" v-bind:b-page="bPage" /> 
        </div>
    </div>
</template>
<script>
    'use strict'
    const {
        remote,
        clipboard
    } = require('electron')
    const {
        Menu,
        MenuItem
    } = remote

    var urllib = require('url')

    import Vue from 'vue'
 

    function createPageObject(location) {
        return {
            location: location || 'https://github.com/xiangwan',
            statusText: false,
            title: 'new tab',
            isLoading: false,
            isSearching: false,
            canGoBack: false,
            canGoForward: false,
            canRefresh: false
        }
    }
    export default {
        components: {
            ChromeTabbar: require("./Chrome/ChromeTabbar"),
            ChromeNavbar: require("./Chrome/ChromeNavbar"),
            ChromeFrame: require("./Chrome/ChromeFrame")
        },
        data: function () {
            return {
                pages: [createPageObject()],
                currentPageIndex: 0
            };
        },
        computed: {
            tabbar: function () {
                return {
                    pages: this.pages,
                    currentPageIndex: this.currentPageIndex,
                    onNewTab: this.onNewTab,
                    onTabClick: this.onTabClick,
                    onTabContextMenu: this.onTabContextMenu,
                    onTabClose: this.onTabClose,
                    onMaximize: this.onMaximize,
                    onMinimize: this.onMinimize,
                    onClose: this.onClose
                };
            },
            navbar: function () {
                return {
                    pages: this.pages,
                    currentPageIndex: this.currentPageIndex,
                    onClickHome: this.onClickHome,
                    onClickBack: this.onClickBack,
                    onClickForward: this.onClickForward,
                    onClickRefresh: this.onClickRefresh,
                    onClickBundles: this.onClickBundles,
                    onClickVersions: this.onClickVersions,
                    onClickSync: this.onClickSync,
                    onEnterLocation: this.onEnterLocation,
                    onChangeLocation: this.onChangeLocation,
                    onLocationContextMenu: this.onLocationContextMenu,

                };
            },
            bPages: function () {
                var self = this;
                return this.pages.map(function (page, i) {
                    if (!page)
                        return;

                    return {
                        pageIndex: i,
                        pages: self.pages,
                        currentPageIndex: self.currentPageIndex,
                        onDidStartLoading: self.onDidStartLoading,
                        onDomReady: self.onDomReady,
                        onDidStopLoading: self.onDidStopLoading,
                        onPageTitleSet: self.onPageTitleSet,
                        onContextMenu: self.onContextMenu,
                        onIpcMessage: self.onIpcMessage
                    };
                });
            }
        },
        mounted: function () {
            // attach webview events
            for (var k in this.webviewHandlers)
                this.getWebView().addEventListener(k, this.webviewHandlers[k].bind(this))

            // attach keyboard shortcuts
            // :TODO: replace this with menu hotkeys
            var self = this
            document.body.addEventListener('keydown', function (e) {
                if (e.metaKey && e.keyCode == 70) { // cmd+f
                    // start search
                    self.getPageObject().isSearching = true

                    // make sure the search input has focus
                    self.getPage().$el.querySelector('#browser-page-search input').focus()
                } else if (e.keyCode == 27) { // esc
                    // stop search
                    self.getPageObject().isSearching = false
                }
            })
        },
        methods: {
            getWebView: function (i) {
                i = (typeof i == 'undefined') ? this.currentPageIndex : i;
                return this.$refs['page-' + i][0].$refs.webview;
            },
            getPage: function (i) {
                i = (typeof i == 'undefined') ? this.currentPageIndex : i;
                return this.$refs['page-' + i][0];
            },
            getPageObject: function (i) {
                i = (typeof i == 'undefined') ? this.currentPageIndex : i;
                return this.pages[i];
            },

            createTab: function (location) {
                this.pages.push(createPageObject(location))
                this.currentPageIndex = this.pages.length - 1;
            },
            closeTab: function (pageIndex) {

                //最后一个tab不允许删除
                if (this.pages.length <= 1) {
                    return;
                }

                this.pages.splice(pageIndex, 1);

                // find the nearest adjacent page to make active
                if (this.currentPageIndex == pageIndex) {
                    for (var i = pageIndex; i >= 0; i--) {
                        if (this.pages[i])
                            return this.currentPageIndex = i;
                    }
                    for (var i = pageIndex; i < this.pages.length; i++) {
                        if (this.pages[i])
                            return this.currentPageIndex = i;
                    }
                }
            },

            tabContextMenu: function (pageIndex) {
                var self = this
                var menu = new Menu()
                menu.append(new MenuItem({
                    label: 'New Tab',
                    click: function () {
                        self.createTab()
                    }
                }))
                menu.append(new MenuItem({
                    label: 'Duplicate',
                    click: function () {
                        self.createTab(self.getPageObject(pageIndex).location)
                    }
                }))
                menu.append(new MenuItem({
                    type: 'separator'
                }))
                menu.append(new MenuItem({
                    label: 'Close Tab',
                    click: function () {
                        self.closeTab(pageIndex)
                    }
                }))
                menu.popup(remote.getCurrentWindow())
            },
            locationContextMenu: function (el) {
                var self = this
                var menu = new Menu()
                menu.append(new MenuItem({
                    label: 'Copy',
                    click: function () {
                        clipboard.writeText(el.value)
                    }
                }))
                menu.append(new MenuItem({
                    label: 'Cut',
                    click: function () {
                        clipboard.writeText(el.value.slice(el.selectionStart, el.selectionEnd))
                        self.getPageObject().location = el.value.slice(0, el.selectionStart) +
                            el.value
                            .slice(el.selectionEnd)
                    }
                }))
                menu.append(new MenuItem({
                    label: 'Paste',
                    click: function () {
                        var l = el.value.slice(0, el.selectionStart) + clipboard.readText() +
                            el.value
                            .slice(el.selectionEnd)
                        self.getPageObject().location = l
                    }
                }))
                menu.append(new MenuItem({
                    label: 'Paste and Go',
                    click: function () {
                        var l = el.value.slice(0, el.selectionStart) + clipboard.readText() +
                            el.value
                            .slice(el.selectionEnd)
                        self.getPageObject().location = l
                        self.getPage().navigateTo(l)
                    }
                }))
                menu.popup(remote.getCurrentWindow())
            },
            webviewContextMenu: function (e) {
                var self = this
                var menu = new Menu()
                if (e.href) {
                    menu.append(new MenuItem({
                        label: 'Open Link in New Tab',
                        click: function () {
                            self.createTab(e.href)
                        }
                    }))
                    menu.append(new MenuItem({
                        label: 'Copy Link Address',
                        click: function () {
                            clipboard.writeText(e.href)
                        }
                    }))
                }
                if (e.img) {
                    menu.append(new MenuItem({
                        label: 'Save Image As...',
                        click: function () {
                            alert('todo')
                        }
                    }))
                    menu.append(new MenuItem({
                        label: 'Copy Image URL',
                        click: function () {
                            clipboard.writeText(e.img)
                        }
                    }))
                    menu.append(new MenuItem({
                        label: 'Open Image in New Tab',
                        click: function () {
                            self.createTab(e.img)
                        }
                    }))
                }
                if (e.hasSelection)
                    menu.append(new MenuItem({
                        label: 'Copy',
                        click: function () {
                            self.getWebView().copy()
                        }
                    }))
                menu.append(new MenuItem({
                    label: 'Select All',
                    click: function () {
                        self.getWebView().selectAll()
                    }
                }))
                menu.append(new MenuItem({
                    type: 'separator'
                }))
                menu.append(new MenuItem({
                    label: 'Inspect Element',
                    click: function () {
                        self.getWebView().inspectElement(e.x, e.y)
                    }
                }))
                menu.popup(remote.getCurrentWindow())
            },


            onNewTab: function () {
                this.createTab()
            },
            onTabClick: function (e, page, pageIndex) {
                this.currentPageIndex = pageIndex;
            },
            onTabContextMenu: function (e, page, pageIndex) {
                this.tabContextMenu(pageIndex)
            },
            onTabClose: function (e, page, pageIndex) {
                this.closeTab(pageIndex)
            },
            onMaximize: function () {
                if (remote.getCurrentWindow())
                    remote.getCurrentWindow().maximize()
                else
                    remote.unmaximize()
            },
            onMinimize: function () {
                remote.getCurrentWindow().minimize()
            },
            onClose: function () {
                remote.getCurrentWindow().close()
            },


            onClickHome: function () {
                this.getWebView().goToIndex(0)
            },
            onClickBack: function () {
                this.getWebView().goBack()
            },
            onClickForward: function () {
                this.getWebView().goForward()
            },
            onClickRefresh: function () {
                this.getWebView().reload()
            },
            onClickBundles: function () {
                var location = urllib.parse(this.getWebView().getURL()).path
                this.getPage().navigateTo('/bundles/view.html#' + location)
            },
            onClickVersions: function () {
                var location = urllib.parse(this.getWebView().getURL()).path
                this.getPage().navigateTo('/bundles/versions.html#' + location)
            },
            onClickSync: console.log.bind(console, 'sync'),
            onEnterLocation: function (location) {
                this.getPage().navigateTo(location)
            },
            onChangeLocation: function (location) {
                var page = this.getPageObject()
                page.location = location
            },
            onLocationContextMenu: function (e) {
                this.locationContextMenu(e.target)
            },

            onDidStartLoading: function (e, page) {
                page.isLoading = true
                page.title = false
            },
            onDomReady: function (e, page, pageIndex) {
                var webview = this.getWebView(pageIndex)
                page.canGoBack = webview.canGoBack()
                page.canGoForward = webview.canGoForward()
                page.canRefresh = true
            },
            onDidStopLoading: function (e, page, pageIndex) {
                // update state
                var webview = this.getWebView(pageIndex)
                page.statusText = false
                page.location = webview.getURL()
                page.canGoBack = webview.canGoBack()
                page.canGoForward = webview.canGoForward()
                if (!page.title)
                    page.title = page.location
                page.isLoading = false
            },
            onPageTitleSet: function (e) {
                var page = this.getPageObject()
                page.title = e.title
                page.location = this.getWebView().getURL()
            },
            onContextMenu: function (e, page, pageIndex) {
                console.log(e);
                this.getWebView(pageIndex).send('get-contextmenu-data', {
                    x: e.offsetX,
                    y: e.offsetY
                })
            },
            onIpcMessage: function (e, page) {
                if (e.channel == 'status') {
                    page.statusText = e.args[0]
                } else if (e.channel == 'contextmenu-data') {
                    this.webviewContextMenu(e.args[0])
                }
            }

        }
    }
</script>
<style scoped>
    .app {
        display: flex;
        flex-direction: column;
        height: 100%;
        border-radius: var(--border-radius);
        border: 1px solid #dbdbdb;
        box-shadow: 0 0 20px #aaa;
    }
    
    .content {
        flex-grow: 1;
        display: flex; 
    }
</style>