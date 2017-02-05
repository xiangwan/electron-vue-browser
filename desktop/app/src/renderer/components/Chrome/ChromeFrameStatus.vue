<template>
    <div id="browser-page-status" :class="className">{{statusText}}</div>
</template>
<script>
    export default {
        props: ['status'],
        computed: {
            statusText: function () {
                var status = this.status.page.statusText;
                if (!status || status == "false") {
                    status = "";
                }
                if (!status && this.status.page.isLoading) {
                    status = 'Loading...';
                }
                return status;
            },
            className: function () {
                return this.statusText == "" ? "hidden" : "visible";
            }
        }
    }
</script>
<style scoped>
    #browser-page-status {
        position: fixed;
        bottom: 0;
        left: 0;
        z-index: 10000;
        padding: 2px 10px;
        background-color: rgb(201, 201, 201);
        color: #555;
        font-size: 16px; 
        font-weight: 100;
    }
</style>