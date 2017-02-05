<template>
    <div class="container webkit-draggable">
        <div class="titlebar-stoplight">
            <!-- 
               <div class="titlebar-close" @click="tabbar.onClose">
                <svg x="0px" y="0px" viewBox="0 0 6.4 6.4">
                    <polygon fill="#4d0000" points="6.4,0.8 5.6,0 3.2,2.4 0.8,0 0,0.8 2.4,3.2 0,5.6 0.8,6.4 3.2,4 5.6,6.4 6.4,5.6 4,3.2"></polygon>
                </svg>
            </div>
            <div class="titlebar-minimize" @click="tabbar.onMinimize">
                <svg x="0px" y="0px" viewBox="0 0 8 1.1">
                    <rect fill="#995700" width="8" height="1.1"></rect>
                </svg>
            </div>
            <div class="titlebar-fullscreen" @click="tabbar.onMaximize">
                <svg class="fullscreen-svg" x="0px" y="0px" viewBox="0 0 6 5.9">
                    <path fill="#006400" d="M5.4,0h-4L6,4.5V0.6C5.7,0.6,5.3,0.3,5.4,0z"></path>
                    <path fill="#006400" d="M0.6,5.9h4L0,1.4l0,3.9C0.3,5.3,0.6,5.6,0.6,5.9z"></path>
                </svg>
                <svg class="maximize-svg" x="0px" y="0px" viewBox="0 0 7.9 7.9">
                    <polygon fill="#006400" points="7.9,4.5 7.9,3.4 4.5,3.4 4.5,0 3.4,0 3.4,3.4 0,3.4 0,4.5 3.4,4.5 3.4,7.9 4.5,7.9 4.5,4.5"></polygon>
                </svg>
            </div>
            -->
        </div>
        <ul class="tabs">
            <chrome-tab v-for="(tab, index) in tabs" v-bind:index="index" v-bind:tab="tab" />
            <div @click="tabbar.onNewTab" class="add" />
        </ul>
        <div class="widget">
            向晚
        </div>
    </div>
</template>
<script>
    import ChromeTab from "./ChromeTab"

    export default {
        props: ['tabbar'],
        computed: {
            tabs: function () {
                var self = this;
                return this.tabbar.pages.map(function (page, i) {
                    if (!page)
                        return

                    function onClick(e) {
                        self.tabbar.onTabClick(e, page, i)
                    }

                    function onContextMenu(e) {
                        self.tabbar.onTabContextMenu(e, page, i)
                    }

                    function onClose(e) {
                        e.preventDefault();
                        e.stopPropagation();
                        self.tabbar.onTabClose(e, page, i)
                    }
                    let isActive = self.tabbar.currentPageIndex == i;

                    return {
                        onClick,
                        onContextMenu,
                        onClose,
                        page,
                        isActive
                    }
                })
            }
        },
        components: {
            ChromeTab
        }
    }
</script>
<style scoped>
     :root {
        --background: #dbdbdb;
        --border: #bbb;
        --border-radius: 4px;
    }
    
    .container {
        display: flex;
        align-items: center;
        background: var(--background);
        border-top-left-radius: var(--border-radius);
        border-top-right-radius: var(--border-radius);
    }
    
    .tabs {
        flex-grow: 1;
        display: flex;
        border-top-left-radius: var(--border-radius);
        border-top-right-radius: var(--border-radius);
        height: 36px;
        padding: 8px 20px 0 12px;
    }
    
    .add {
        background: #d0d0d0;
        width: 26px;
        height: 15px;
        border-radius: 2px;
        margin-left: 8px;
        border: 1px solid var(--border);
        align-self: center;
        transform: skewx(25deg);
        &:hover {
            background: #d9d9d9;
        }
    }
    
    .widget {
        margin: 0 8px;
        height: 20px;
        font-size: 12px;
    }
    
    .titlebar-stoplight {
         width:65px;
        flex-grow: 0;
        display: flex;
    }
    
    .container.webkit-draggable {
        -webkit-app-region: drag;
    }
    
    .container.alt svg.fullscreen-svg {
        display: none;
    }
    
    .container.alt svg.maximize-svg {
        display: block !important;
    }
    
    .container.webkit-draggable .titlebar-close,
    .container.webkit-draggable .titlebar-minimize,
    .container.webkit-draggable .titlebar-fullscreen {
        -webkit-app-region: no-drag;
    }
    
    .titlebar-stoplight .titlebar-close,
    .titlebar-stoplight .titlebar-minimize,
    .titlebar-stoplight .titlebar-fullscreen {
        width: 12px;
        height: 12px;
        border-radius: 50%;
        margin: 6px 4px;
        line-height: 0;
    }
    
    .titlebar-stoplight .titlebar-close {
        border: 1px solid #e2463f;
        background-color: #ff5f57;
        margin-left: 6px;
    }
    
    .titlebar-stoplight .titlebar-close:active {
        border-color: #ad3934;
        background-color: #bf4943;
    }
    
    .titlebar-stoplight .titlebar-close svg {
        width: 6px;
        height: 6px;
        margin-top: 2px;
        margin-left: 2px;
        opacity: 0;
    }
    
    .titlebar-stoplight .titlebar-minimize {
        border: 1px solid #e1a116;
        background-color: #ffbd2e;
    }
    
    .titlebar-stoplight .titlebar-minimize:active {
        border-color: #ad7d15;
        background-color: #bf9123;
    }
    
    .titlebar-stoplight .titlebar-minimize svg {
        width: 8px;
        height: 8px;
        margin-top: 1px;
        margin-left: 1px;
        opacity: 0;
    }
    
    .titlebar-stoplight .titlebar-fullscreen,
    .titlebar-stoplight .titlebar-maximize {
        border: 1px solid #12ac28;
        background-color: #28c940;
    }
    
    .titlebar-stoplight .titlebar-fullscreen:active {
        border-color: #128622;
        background-color: #1f9a31;
    }
    
    .titlebar-stoplight .titlebar-fullscreen svg.fullscreen-svg {
        width: 6px;
        height: 6px;
        margin-top: 2px;
        margin-left: 2px;
        opacity: 0;
    }
    
    .titlebar-stoplight .titlebar-fullscreen svg.maximize-svg {
        width: 8px;
        height: 8px;
        margin-top: 1px;
        margin-left: 1px;
        opacity: 0;
        display: none;
    }
    
    .titlebar-stoplight:hover svg,
    .titlebar-stoplight:hover svg.fullscreen-svg,
    .titlebar-stoplight:hover svg.maximize-svg {
        opacity: 1;
    }
</style>