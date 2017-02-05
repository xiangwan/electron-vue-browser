<template>
    <li @click="tab.onClick" @contextmenu="tab.onContextMenu" :class="{active: tab.isActive}" class="tab">
        <img :src="tab.page.location==='' ? require( './assets/favicon.svg') : getFavicon()" class="favicon" alt="favicon" />
        <div class="content"> {{title}}</div>
        <svg viewBox="0 0 24 24" class="close" @click="tab.onClose">
            <path d="M19 6.41L17.59 5 12 10.59 6.41 5 5 6.41 10.59 12 5 17.59 6.41 19 12 13.41 17.59 19 19 17.59 13.41 12z" />
        </svg>
    </li>
</template>
<script>
    export default {
        props: ['tab'],
        computed: {
            title: function () {
                return this.tab.page.title || 'loading'
            }

        },
        methods: {
            getFavicon: function () {
                return require( "./assets/favicon.svg");
            }
        }
    }
</script>
<style scoped>
     :root {
        --background: #dbdbdb;
        --hover: #eee;
        --border: #bbb;
        --nav: #f2f2f2;
    }
    
    .tab {
        flex-basis: 218px;
        display: flex;
        min-width: 0;
        /* http://stackoverflow.com/questions/34934586/white-space-nowrap-and-flexbox-did-not-work-in-chrome */
        position: relative;
        background-color: var(--background);
        border-top: 1px solid var(--border);
        margin: 0 5px;
        font-size: 0;
        transition: background-color .5s;
        &:hover {
            background: var(--hover);
            &::before,
            &::after {
                background: var(--hover);
            }
        }
        &.active {
            background: var(--nav);
            height: 29px;
            border-bottom: 1px solid var(--nav);
            transition: all 0s;
            &::before,
            &::after {
                z-index: 10;
                transition: all 0s;
                align-self: flex-start;
                height: 28px;
                background: var(--nav);
                border-bottom: 1px solid var(--nav);
            }
        }
        & .favicon {
            z-index: 100;
            align-self: center;
            width: 16px;
            height: 16px;
            margin-left: 4px;
            margin-right: 4px;
            user-select: none;
        }
        & .close {
            fill: #000;
            width: 12px;
            height: 12px;
            border-radius: 50%;
            &:hover {
                fill: #fff;
                background: #f00;
            }
        }
        &::before {
            content: '';
            position: absolute;
            z-index: 0;
            left: 0;
            width: 16px;
            height: 28px;
            background-color: var(--background);
            border-left: 1px solid var(--border);
            border-bottom: 1px solid var(--border);
            transform: skewx(-25deg);
            transform-origin: left top;
            transition: background-color .5s;
        }
        &::after {
            content: '';
            position: absolute;
            z-index: 1;
            right: 0;
            width: 16px;
            height: 28px;
            background-color: var(--background);
            border-right: 1px solid var(--border);
            border-bottom: 1px solid var(--border);
            transform: skewx(25deg);
            transform-origin: right top;
            transition: background-color .5s;
        }
        & .content {
            flex-grow: 1;
            padding-left: 2px;
            font-size: 12.6px;
            line-height: 28px;
            cursor: default;
            max-width: 200px;
            user-select: none;
            white-space: nowrap;
            overflow: hidden;
            text-overflow: clip;
        }
       & .close {
            width: 12px;
            height: 12px;
            border-radius: 50%;
            z-index: 100;
            align-self: center;
            margin-left: 2px;
            margin-right: 2px;
            & path {
                fill: #555;
            }
            &:hover {
                background: #e25c4d;
                & path {
                    fill: #fff;
                }
            }
        }
    }
</style>