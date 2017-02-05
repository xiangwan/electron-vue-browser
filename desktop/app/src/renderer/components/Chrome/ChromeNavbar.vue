<template>
    <div class="nav">
        <a href="#" :class="{disabled: back.disabled}" :title="back.title" @click="back.onClick">
            <svg viewBox="0 0 32 32">
                <path d="M 4.22 14.03 C 3.53 14.15 3 14.77 3 15.5 C 3 16.25 3.53 16.86 4.24 16.98 L 4.02 17.19 L 14.36 27.51 C 14.65 27.81 15.05 28 15.5 28 C 16.33 28 17 27.33 17 26.5 C 17 26.05 16.8 25.65 16.49 25.38 L 16.55 25.32 L 8.22 17 L 27.51 17 C 28.33 17 29 16.33 29 15.5 C 29 14.67 28.33 14 27.51 14 L 8.2 14 L 16.55 5.66 C 16.81 5.34 17 4.94 17 4.5 C 17 3.68 16.33 3 15.5 3 C 15.06 3 14.66 3.2 14.39 3.5 L 14.36 3.47 L 4 13.81 Z"></path>
            </svg>
        </a>
        <a href="#" :class="{disabled: forward.disabled}" :title="forward.title" @click="forward.onClick">
            <svg viewBox="0 0 32 32">
                <path d="M 27.78 14.03 C 28.47 14.15 29 14.77 29 15.5 C 29 16.25 28.47 16.86 27.76 16.98 L 27.98 17.19 L 17.64 27.51 C 17.35 27.81 16.95 28 16.5 28 C 15.67 28 15 27.33 15 26.5 C 15 26.05 15.2 25.65 15.51 25.38 L 15.45 25.32 L 23.78 17 L 4.49 17 C 3.67 17 3 16.33 3 15.5 C 3 14.67 3.67 14 4.49 14 L 23.8 14 L 15.45 5.66 C 15.19 5.34 15 4.94 15 4.5 C 15 3.68 15.67 3 16.5 3 C 16.94 3 17.34 3.2 17.61 3.5 L 17.64 3.47 L 28 13.81 Z"></path>
            </svg>
        </a>
        <a href="#" :class="{disabled: refresh.disabled}" :title="refresh.title" @click="refresh.onClick">
            <svg viewBox="0 0 32 32">
                <path d="M 25.1 20.15 L 25.08 20.14 C 23.51 23.59 20.04 26 16 26 C 10.48 26 6 21.52 6 16 C 6 10.48 10.48 6 16 6 C 19.02 6 21.72 7.34 23.55 9.45 L 23.55 9.45 L 19 14 L 25.8 14 L 28.83 14 L 30 14 L 30 3 L 25.67 7.33 C 23.3 4.67 19.85 3 16 3 C 8.82 3 3 8.82 3 16 C 3 23.18 8.82 29 16 29 C 21.27 29 25.8 25.86 27.84 21.34 C 27.96 21.13 28.03 20.88 28.03 20.61 C 28.03 19.78 27.36 19.11 26.53 19.11 C 25.87 19.11 25.3 19.55 25.1 20.15 Z"></path>
            </svg>
        </a>
        <form :class={active:formFocus}>
            <chrome-navbar-location @focus="onLocationFocus" @blur="onLocationBlur" :loc="loc" />
            <svg viewBox="0 0 16 16">
                <path d="M 8 4 L 9.12 6.79 L 12 7.05 L 9.82 9.04 L 10.47 12 L 8 10.43 L 5.53 12 L 6.18 9.04 L 4 7.05 L 6.88 6.79 L 8 4 M 8 0.5 L 5.89 5.56 L 0.5 6.03 L 4.59 9.64 L 3.37 15 L 8 12.16 L 12.64 15 L 11.41 9.64 L 15.5 6.03 L 10.11 5.56 L 8 0.5 Z"></path>
            </svg>
        </form>
        <a>
            <svg viewBox="0 0 16 16">
                <path d="M 7 3.5 C 7 2.67 7.67 2 8.5 2 C 9.33 2 10 2.67 10 3.5 C 10 4.33 9.33 5 8.5 5 C 7.67 5 7 4.33 7 3.5 Z M 7 8.5 C 7 7.67 7.67 7 8.5 7 C 9.33 7 10 7.67 10 8.5 C 10 9.33 9.33 10 8.5 10 C 7.67 10 7 9.33 7 8.5 Z M 7 13.5 C 7 12.67 7.67 12 8.5 12 C 9.33 12 10 12.67 10 13.5 C 10 14.33 9.33 15 8.5 15 C 7.67 15 7 14.33 7 13.5 Z"></path>
            </svg>
        </a>
    </div>ÍÍ
</template>
<script>
    export default {
        props: ['navbar'],
        data() {
            return {
                formFocus: false
            }
        },
        methods: {
            onLocationFocus: function () {
                console.log(" nav input focus ")
                this.formFocus = true;
            },
            onLocationBlur: function () {
                this.formFocus = false;
            }
        },
        computed: {
            title: function () {
                return this.page.title || 'loading'
            },
            page: function () {
                return this.navbar.pages[this.navbar.currentPageIndex];
            },
            loc: function () {
                return {
                    onEnterLocation: this.navbar.onEnterLocation,
                    onChangeLocation: this.navbar.onChangeLocation,
                    onContextMenu: this.navbar.onLocationContextMenu,
                    page: this.page
                };
            },
            rewind: function () {
                return {
                    title: "Rewind",
                    icon: "angle-double-left fa-lg",
                    onClick: this.navbar.onClickHome,
                    disabled: !this.page.canGoBack
                }
            },
            back: function () {
                return {
                    title: "Back",
                    icon: "angle-left fa-lg",
                    onClick: this.navbar.onClickBack,
                    disabled: !this.page.canGoBack
                }
            },
            forward: function () {
                return {
                    title: "Forward",
                    icon: "angle-right fa-lg",
                    onClick: this.navbar.onClickForward,
                    disabled: !this.page.canGoForward
                }
            },
            refresh: function () {
                return {
                    title: "Refresh",
                    icon: "circle-thin",
                    onClick: this.navbar.onClickRefresh,
                    disabled: !this.page.canRefresh
                }
            }
        },
        components: {
            ChromeNavbarLocation: require("./ChromeNavbarLocation")
        }
    }
</script>
<style scoped>
     :root {
        --hover: #ccc;
        --border: #bbb;
        --nav: #f2f2f2;
        --icon: #6d6d6d;
        --icon-hover: #dfdfdf;
        --icon-focus: #aaa;
    }
    
    .nav {
        display: flex;
        align-items: center;
        background: var(--nav);
        border-top: 1px solid var(--border);
        border-bottom: 1px solid #ddd;
        padding-left: 4px;
        padding-right: 4px;
        height: 38px;
        & a {
            border-radius: 2px;
            margin-left: 2px;
            margin-right: 2px;
            padding: 4px;
            height: 24px;
            &:hover {
                background: var(--icon-hover);
            }
            ;
            &.disabled {
                &:hover {
                    background: var(--nav);
                    cursor: default;
                }
                & svg {
                    & path {
                        fill: var(--border);
                    }
                }
            }
        }
        & svg {
            width: 16px;
            height: 16px;
            & path {
                fill: var(--icon);
            }
        }
        & form {
            flex-grow: 1;
            display: flex;
            align-items: center;
            background: #fff;
            height: 28px;
            border-radius: 4px;
            margin-left: 4px;
            margin-right: 2px;
            border: 1px solid #ccc;
            &.active {
                border: 1px solid #399df7;
                height: 26px;
            }
            & svg {
                margin-right: 4px;
            }
            & input {
                display: block;
                border: none;
                width: 100%;
                padding-left: 20px;
                font-size: 13.6px;
                &:focus {
                    outline: none;
                }
                &:-webkit-autofill {
                    background-color: #fff;
                }
            }
        }
    }
</style>