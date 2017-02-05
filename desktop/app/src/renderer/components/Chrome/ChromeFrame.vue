<template>
    <div id="browser-page" :class="{hidden:!isActive}">
        <webview ref="webview" :preload="preload" autosize="on" @contextmenu="bPage.onContextMenu" />
        <chrome-frame-status :status="status" />
    </div>
</template>
<script>
    import process from 'process'

    export default {
        props: ['bPage'],
        computed: {
            page: function () {
                return this.bPage.pages[this.bPage.currentPageIndex];
            },
            searchInput: function () {
                return {
                    isActive: this.page.isSearching,
                    onPageSearch: this.onPageSearch
                }
            },
            status: function () {
                return {
                    page: this.page
                }
            },
            isActive: function () {
                return this.bPage.pageIndex == this.bPage.currentPageIndex;
            },
            preload: function () {
                return "file:///" + process.cwd() + "/app/dist/electron/preload/main.js";
            }
        },
        beforeDestroy: function () {
            window.removeEventListener('resize', resize);
        },
        mounted: function () {
            // setup resize events
            window.addEventListener('resize', resize)
            resize()

            // attach webview events
            for (var k in webviewEvents)
                this.$refs.webview.addEventListener(k, webviewHandler(this, webviewEvents[k]))

            // set location, if given
            if (this.page.location)
                this.navigateTo(this.page.location)
        },
        methods: {
            navigateTo: function (l) {
                this.$refs.webview.setAttribute('src', l)
            },

            onPageSearch: function (query) {
                this.$refs.webview.executeJavaScript('window.find("' + query + '", 0, 0, 1)')
            }
        },
        components: {
            ChromeFrameStatus: require("./ChromeFrameStatus")
        }
    }

    function webviewHandler(self, fnName) {
        return function (e) {
            if (self.bPage[fnName])
                self.bPage[fnName](e, self.page, self.bPage.pageIndex)
        }
    }

    var webviewEvents = {
        'load-commit': 'onLoadCommit',
        'did-start-loading': 'onDidStartLoading',
        'did-stop-loading': 'onDidStopLoading',
        'did-finish-load': 'onDidFinishLoading',
        'did-fail-load': 'onDidFailLoad',
        'did-get-response-details': 'onDidGetResponseDetails',
        'did-get-redirect-request': 'onDidGetRedirectRequest',
        'dom-ready': 'onDomReady',
        'page-title-set': 'onPageTitleSet',
        'close': 'onClose',
        'destroyed': 'onDestroyed',
        'ipc-message': 'onIpcMessage',
        'console-message': 'onConsoleMessage'
    }

    function resize() {
        console.log("resize fired.")
        Array.prototype.forEach.call(document.querySelectorAll('webview'), function (webview) {
            var obj = webview && webview.querySelector('::shadow object')
            if (obj)
                obj.style.height = (window.innerHeight - 59) + 'px' // -61 to adjust for the tabs and navbar regions
        })
    }
</script>
<style>
    #browser-page {
        position: relative;
          width: 100%;
            min-height: 100%;
    }
    webview { 
            border: 0;
             width: 100%;
            min-height: 100%;
            /* This fix Edge */
        }
    .hidden {
        display: none;
    }
</style>