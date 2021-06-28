<template>
    <v-container>
        <div :style="{ 'width': width, 'height': height }" ref="viewer">
            <div class="wrapper">
                <div
                    class="flipbook-reader-area"
                    ref="flipbookReader"
                    @click="bookClick"
                    :style="{ 'width': width, 'height': height }" />
                <div class="flipbook bookControl" :style="{ visibility: controlIsVisible ? '' : 'hidden' }">
                    <v-btn
                        @click="leftClick"
                        class="mx-2 book-button book-button-left"
                        :style="{ 'top': bookButtonTop }" >
                        <v-icon dark>
                            fa-reply
                        </v-icon>
                    </v-btn>
                    <v-btn
                        @click="rightClick"
                        class="mx-2 book-button book-button-right"
                        :style="{ 'top': bookButtonTop }">
                        <v-icon dark>
                            fa-share
                        </v-icon>
                    </v-btn>
                </div>
            </div>
            <flipbook 
                ref="flipbook" 
                class="flipbook book"
                :pages="pages"
                :startPage="startPage"
                v-slot="flipbook"
                :style="{ 'width': width, 'height': height }">
                <div class="flipbook-head">
                    <button ref="flipbookLeft" @click="flipbook.flipLeft"></button>
                    <button ref="flipbookRight" @click="flipbook.flipRight"></button>
                </div>
            </flipbook>
        </div>
    </v-container>
</template>

<script>
import Flipbook from "flipbook-vue";

export default {
    name: 'Viewer',
    components: {
        Flipbook
    },
    props: {
        id: {
            default: -1,
            type: Number
        },
        pages: {
            default: null,
            type: Array
        },
        startPage: {
            default: 0,
            type: Number
        },
        width: {
            default: "90vh",
            type: String
        },
        height: {
            default: "90vh",
            type: String
        }
    },
    data() {
        return {
            controlIsVisible: false,
            touchStartPoiont: null,
            bookButtonTop: 0
        };
    },
    computed: {
    },
    watch: {
    },
    mounted() {
        this.$refs.flipbookReader.addEventListener('touchstart', this.touchstart);
        this.$refs.flipbookReader.addEventListener('touchmove', this.touchmove);
        this.$refs.flipbookReader.addEventListener('touchend', this.touchend);
    },
    methods: {
        bookClick() {
            const rect = this.$refs.flipbook.$el.getBoundingClientRect();
            this.bookButtonTop = (rect.height / 2) + 'px';
            this.controlIsVisible = !this.controlIsVisible;
            this.$emit("onControlVisible", this.controlIsVisible);
        },
        leftClick() {
            this.$refs.flipbookLeft.click();
        },
        rightClick() {
            this.$refs.flipbookRight.click();
        },
        touchstart(e) {
            this.touchStartPoiont = {
                x: e.touches[0].pageX,
                y: e.touches[0].pageY
            };
        },
        touchmove(e) {
            if (!this.touchStartPoiont || !this.touchStartPoiont.x) return;
            let isLeft = e.touches[0].pageX < this.touchStartPoiont.x;
            if (isLeft) {
                this.leftClick();
                return;
            }
            this.rightClick();
        },
        touchend() {
            if (!this.touchStartPoiont) return;
            this.touchStartPoiont = null;
        },
    },
    beforeDestroy() {
        this.$refs.flipbookReader.removeEventListener('touchstart', this.touchstart);
        this.$refs.flipbookReader.removeEventListener('touchmove', this.touchmove);
        this.$refs.flipbookReader.removeEventListener('touchend', this.touchend);
    }
}
</script>

<style scoped>
.flipbook {
    position: absolute;
    left: 0;
    right: 0;
    margin: auto;
}
.flipbook-head {
    text-align: center;
}
.flipbook-reader-area {
    position: absolute;
    z-index: 1000;
}
.wrapper {
    z-index: 999;
}
.bookControl {
    position: absolute;
    z-index: 1001;
}
.book-button {
    position: absolute;
    z-index: 1002;
}
.book-button-left {
    left: 0;
}
.book-button-right {
    right: 0;
}
.book {
    z-index: 0
}
</style>
