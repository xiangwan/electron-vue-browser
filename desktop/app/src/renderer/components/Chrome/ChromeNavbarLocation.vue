<template>
    <input type="text" @keydown="onKeyDown" @change="onChange" @focus="onFocus" @blur="onBlur" @contextmenu="loc.onContextMenu" :value="loc.page.location" />
</template>
<script>
    function normalizedUri(input) {
        var prefix = 'http://';

        if (!/^([^:\/]+)(:\/\/)/g.test(input) && !prefix.includes(input)) {
            input = prefix + input;
        }

        return input;
    }

    export default {
        props: ["loc"],
        methods: {
            onKeyDown: function (e) {
                if (e.keyCode == 13)
                    this.loc.onEnterLocation(normalizedUri(e.target.value))
            },
            onChange: function (e) {
                this.loc.onChangeLocation(normalizedUri(e.target.value))
            },
            onFocus:function(e){
                this.$emit("focus");
            },
             onBlur:function(e){
                this.$emit("blur");
            }
        }
    }
</script>