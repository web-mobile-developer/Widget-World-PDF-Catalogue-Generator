<template>
    <div class="product-list" :class="pageClass">
        <div class="row product-body">
            <div class="col-6 nopadding page-separator">
                <div v-if="selectedPage > 1" class="page-body">
                    <div v-for="rowInd in pageRows" class="row" v-bind:key="rowInd">
                        <div class="nopadding"
                             v-for="colInd in getCols()"
                             v-bind:key="colInd"
                             :class="colClass">
                            <div class="product-image" v-if="checkNewBlock(rowInd, colInd, 0) == 'block'">
                                <div class="new-block">
                                    <div v-html="getProductTitle(rowInd, colInd, 0)" />
                                </div>
                            </div>
                            <div class="product-image product-logo" v-else-if="checkNewBlock(rowInd, colInd, 0) == 'logo'">
                                <img :src="getImgUrl(rowInd, colInd, 0)" />
                            </div>
                            <div class="product-image" v-else-if="getImgUrl(rowInd, colInd, 0)" >
                                <div class="ribbon" v-if="checkNewState(rowInd, colInd, 0)" :class="{active: checkNewState(rowInd, colInd, 0)}">NEW</div>
                                <img :src="getImgUrl(rowInd, colInd, 0)"/>
                                <div class="product-box">
                                    <div class="product-title"
                                        v-if="catalogue.display_options.indexOf('title')>= 0" >
                                        {{getProductTitle(rowInd, colInd, 0)}}
                                    </div>
                                    <div>
                                        <div class="product-detail"
                                             v-if="catalogue.display_options.indexOf('units')>= 0">
                                            {{getUnits(rowInd, colInd, 0)}} units per outer
                                        </div>
                                        <div class="product-detail pb-1">
                                            <div v-if="!catalogue.barcode_options">
                                                {{getBarcodeNumber(rowInd, colInd, 0)}}
                                            </div>
                                            <div v-else-if="catalogue.display_options.indexOf('rrp')>= 0" class="redLabelColor">
                                                RRP ${{getRRP(rowInd, colInd, 0)}}
                                            </div>
                                        </div>
                                        <div class="barcode-image">
                                            <img v-if="catalogue.barcode_options"
                                                 :src="getBarcodeImage(rowInd, colInd, 0)" />
                                            <div v-else-if="catalogue.display_options.indexOf('rrp')>= 0" class="product-rrp">
                                                RRP<br/>${{getRRP(rowInd, colInd, 0)}}
                                            </div>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
                <div v-if="selectedPage > 1" class="page-footer">
                    <span class="page-label">Page {{selectedPage}}</span>
                    <span class="pull-right pr-3">{{catalogue.name}}</span>
                </div>
                <div v-else class="preview">
                    <img :src="$store.state.catalogue.coverPath" class="preview-background"/>
                    <div class="preview-bottom">
                        <div class="preview-content">
                            <img :src="catalogue.file_upload_path ? catalogue.file_upload_path : catalogue.logo_url" v-if="catalogue.file_upload_path || catalogue.logo_url" class="upload-image"/>
                            <div v-if="catalogue.name" class="preview-title text-white">{{catalogue.name}}</div>
                        </div>
                    </div>
                </div>
            </div>
            <div class="col-6 nopadding">
                <div class="page-body">
                    <div v-for="rightRow in pageRows" class="row" v-bind:key="rightRow">
                        <div class="nopadding"
                             v-for="rightCol in getCols(catalogue.page_columns)"
                             v-bind:key="rightCol"
                             :class="colClass">
                            <div class="product-image" v-if="checkNewBlock(rightRow, rightCol, 1) == 'block'">
                                <div class="new-block">
                                    <div v-html="getProductTitle(rightRow, rightCol, 1)" />
                                </div>
                            </div>
                            <div class="product-image product-logo" v-else-if="checkNewBlock(rightRow, rightCol, 1) == 'logo'">
                                <img :src="getImgUrl(rightRow, rightCol, 1)" />
                            </div>
                            <div class="product-image" v-else-if="getImgUrl(rightRow, rightCol, 1)" >
                                <div class="ribbon" v-if="checkNewState(rightRow, rightCol, 1)" :class="{active: checkNewState(rightRow, rightCol, 1)}">NEW</div>
                                <img :src="getImgUrl(rightRow, rightCol, 1)"/>
                                <div class="product-box">
                                    <div class="product-title"
                                         v-if="catalogue.display_options.indexOf('title')>= 0" >
                                        {{getProductTitle(rightRow, rightCol, 1)}}
                                    </div>
                                    <div>
                                        <div class="product-detail"
                                             v-if="catalogue.display_options.indexOf('units')>= 0">
                                            {{getUnits(rightRow, rightCol, 1)}} units per outer
                                        </div>
                                        <div class="product-detail pb-1">
                                            <div v-if="!catalogue.barcode_options">
                                                {{getBarcodeNumber(rightRow, rightCol, 1)}}
                                            </div>
                                            <div v-else-if="catalogue.display_options.indexOf('rrp')>= 0" class="redLabelColor">
                                                RRP ${{getRRP(rightRow, rightCol, 1)}}
                                            </div>
                                        </div>
                                        <div class="barcode-image">
                                            <img v-if="catalogue.barcode_options"
                                                 :src="getBarcodeImage(rightRow, rightCol, 1)" />
                                            <div v-else-if="catalogue.display_options.indexOf('rrp')>= 0" class="product-rrp">
                                                RRP<br/>${{getRRP(rightRow, rightCol, 1)}}
                                            </div>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="page-footer">
                    <span class="pl-3 right-label">{{catalogue.name}}</span>
                    <span class="page-label pull-right">Page {{selectedPage + 1}}</span>
                </div>
            </div>
        </div>
        <div class="product-footer">
            <label class="pull-left ml-2" :class="{active: selectedPage > 1}" @click="prevPage">
                <i class="fa fa-chevron-left" aria-hidden="true"></i> Previous
            </label>
            <label class="page-nav">
                <span :class="{active: getNavIndex() == 0}" @click="updatePageNav(0)" v-if="getPageNav(0)">{{getPageNav(0)}}</span>
                <span :class="{active: getNavIndex() == 1}" @click="updatePageNav(1)" v-if="getPageNav(1)">{{getPageNav(1)}}</span>
                <span :class="{active: getNavIndex() == 2}" @click="updatePageNav(2)" v-if="getPageNav(2)">{{getPageNav(2)}}</span>
                <span :class="{active: getNavIndex() == 3}" @click="updatePageNav(3)" v-if="getPageNav(3)">{{getPageNav(3)}}</span>
            </label>
            <label class="pull-right mr-2" :class="{active: selectedPage < (totalPages - 1)}" @click="nextPage">
                Next <i class="fa fa-chevron-right" aria-hidden="true"></i>
            </label>
        </div>
    </div>
</template>
<script>
    export default {
        name: "product_preview",
        components: {
        },
        props: ['catalogue', 'productData', 'totalPages'],
        data() {
            return {
                selectedPage: 0,
                pageClass: '',
                pageRows: 3,
                colClass: 'col-4',
            }
        },
        mounted: function () {
            this.pageInit();
        },
        watch: {
            totalPages() {
                this.pageInit();
            }
        },
        methods: {
            pageInit() {
                this.selectedPage = 0;
                let cols = parseInt(this.catalogue.page_columns);
                switch (cols) {
                    case 2:
                        this.pageClass = 'twoPages';
                        this.pageRows = 3;
                        this.colClass = 'col-6';
                        break;
                    case 3:
                        this.pageClass = '';
                        this.pageRows = 3;
                        this.colClass = 'col-4';
                        break;
                    case 4:
                        this.pageClass = 'fourPages';
                        this.pageRows = 4;
                        this.colClass = 'col-3';
                        break;
                }
                let coverIndex = this.catalogue.selectedImage ? this.catalogue.selectedImage : this.catalogue.cover_index;
                if (!coverIndex) coverIndex = 0;
                this.$store.state.catalogue.coverPath = require('../../assets/img/covers/blank.jpg');
                if (coverIndex) {
                    let app = this;
                    axios.get('/api/getCovers', {params: {id: coverIndex}}).then(response => {
                        if (response && response.data) {
                            app.$store.state.catalogue.coverPath = response.data;
                        }
                    });
                }
            },
            prevPage() {
                this.selectedPage -= 2;
                if (this.selectedPage < 0) this.selectedPage = 0;
            },
            getNavIndex() {
                return this.selectedPage / 2 % 4;
            },
            updatePageNav(index) {
                this.selectedPage += (index - this.getNavIndex()) * 2;
            },
            getPageNav(index) {
                let firstPageNumber = this.selectedPage + 1 + (index - this.getNavIndex()) * 2;
                if (firstPageNumber > (this.totalPages + 1)) return;
                return firstPageNumber + "-" + (firstPageNumber + 1);
            },
            nextPage() {
                if (this.selectedPage >= (this.totalPages - 1)) return;
                this.selectedPage += 2;
            },
            getImgUrl(rowInd, colInd, backPage) {
                let index = this.getIndex(rowInd, colInd, backPage);
                if (this.productData[index] && this.productData[index]['name']) {
                    let imageUrl = this.productData[index]['images'];
                    if (!imageUrl) imageUrl = require('../../assets/img/products/empty.jpg');
                    return imageUrl;
                } else {
                    return false;
                }
            },
            checkNewState(rowInd, colInd, backPage) {
                return this.getProductInfo(rowInd, colInd, backPage, 'product_is_new')
            },
            getIndex(rowInd, colInd, backPage) {
                let rows = 3;
                if (this.catalogue.page_columns == 4) rows = 4;
                let index = (this.selectedPage - 1 + backPage) * rows * this.catalogue.page_columns;
                index += (rowInd - 1) * this.catalogue.page_columns + colInd - 1;
                return index;
            },
            getProductInfo(rowInd, colInd, backPage, type, failure = '') {
                let index = this.getIndex(rowInd, colInd, backPage);
                if (this.productData[index] && this.productData[index][type]) {
                    return this.productData[index][type];
                } else {
                    return failure;
                }
            },
            checkNewBlock(rowInd, colInd, backPage) {
                return this.getProductInfo(rowInd, colInd, backPage, 'type');
            },
            getProductTitle(rowInd, colInd, backPage) {
                return this.getProductInfo(rowInd, colInd, backPage, 'name');
            },
            getBarcodeImage(rowInd, colInd, backPage) {
                let failure = require("../../assets/img/products/barcode.png");
                return this.getProductInfo(rowInd, colInd, backPage, 'barcode_image', failure);
            },
            getUnits(rowInd, colInd, backPage) {
                return this.getProductInfo(rowInd, colInd, backPage, 'items_per_outer', 0);
            },
            getRRP(rowInd, colInd, backPage) {
                return this.getProductInfo(rowInd, colInd, backPage, 'rrp', '0.00');
            },
            getBarcodeNumber(rowInd, colInd, backPage) {
                return this.getProductInfo(rowInd, colInd, backPage, 'barcode_unit');
            },
            getCols() {
                let cols = [];
                for (let i=1; i<=this.catalogue.page_columns;i++) {
                    cols.push(i);
                }
                return cols;
            },
            getCoverImage(index) {
                console.log("aaa ", index)
                return require('../../assets/img/covers/' + this.imageList[index]);
            },
        }
    }
</script>
<style lang="scss" scoped>
    @import "../layouts/css/customvariables";
    .product-list {
        min-height: 830px !important;
        position: relative;
        .product-body {
            min-height: 785px !important;
            background: $white_color;
            border: 2px solid $border_color;
            .page-separator {
                border-right: 2px solid $border_color;
            }
            .page-body {
                height: 100%;
                padding: 15px 15px 40px;
                .product-logo {
                    display: flex;
                    flex-direction: column;
                    justify-content: center;
                    border: none !important;
                }
                .product-image {
                    position: relative;
                    height: calc(100% - 10px);
                    min-height: 232px;
                    margin: 5px;
                    border: 1px solid $border_color;
                    overflow: hidden;
                    z-index: 999;
                    text-align: center;
                    img {
                        max-width: 100%;
                        max-height: 58%;
                        width: auto;
                        margin: 10px auto 0;
                    }
                    .ribbon {
                        text-align: center;
                        transform: rotate(45deg);
                        padding: 3px 0;
                        top: 5px;
                        right: -20px;
                        width: 75px;
                        background-color: $menu_active;
                        color: #fff;
                        position: absolute;
                        font-size: 12px;
                        font-weight: bold;
                        z-index: 1;
                        cursor: pointer;
                    }
                    .ribbon.active {
                        background-color: $red_label_color;
                    }
                    .new-block {
                        height: 100%;
                        display: flex;
                        align-items: center;
                        justify-content: center;
                    }
                    .product-box {
                        position: absolute;
                        width: 100%;
                        bottom: 0;
                        text-align: left;
                        .product-title {
                            min-height: 36px;
                            text-align: center;
                            padding: 0 5px;
                            font-size: 13px;
                            font-weight: bold;
                            position: absolute;
                            bottom: 42px;
                            width: 100%;
                            overflow: hidden;
                            text-overflow: ellipsis;
                            display: -webkit-box;
                            line-height: 14px;
                            -webkit-line-clamp: 3;
                            -webkit-box-orient: vertical;
                        }
                        .product-detail {
                            max-width: 60%;
                            min-height: 18px;
                            padding-left: 8px;
                            font-size: 12px;
                            line-height: 18px;
                            overflow: hidden;
                            text-overflow: ellipsis;
                            white-space: nowrap;
                        }
                        .barcode-image {
                            position: absolute;
                            bottom: 5px;
                            right: 8px;
                            max-width: 40%;
                            img {
                                max-height: 50px;
                            }
                        }
                        .product-rrp {
                            font-size: 12px;
                            line-height: 16px;
                            color: $red_label_color;
                        }
                    }
                }
            }
            .page-footer {
                position: absolute;
                width: 100%;
                bottom: 10px;
                .page-label {
                    background: $green_color;
                    color: $white_color;
                    padding: 2px 10px;
                }
                .right-label {
                    padding-top: 4px;
                    display: inline-block;
                }
            }
            .preview {
                margin: 10px;
                display: block;
                text-align: center;
                .preview-background {
                    width: 30vw;
                    max-width: 100%;
                    height: auto;
                }
                .preview-bottom {
                    position: absolute;
                    width: calc(100% - 20px);
                    bottom: 100px;
                }
                .preview-content {
                    width: 26.3vw;
                    max-width: calc(100% - 80px);
                    min-width: 200px;
                    margin: 0 auto;
                    .preview-title {
                        background: $red_color;
                        padding: 8px 5px;
                        min-height: 35px;
                        font-size: 16px;
                    }
                    .upload-image {
                        height: 85px;
                        margin-top: -85px;
                        max-height: 6vw;
                    }
                }
            }
        }
        .product-footer {
            position: relative;
            width: 100%;
            bottom: 0;
            margin-top: 5px;
            text-align: center;
            color: $grey_btn_color;
            label.ml-4, label:first-child, label:last-child {
                cursor: pointer;
            }
            .page-nav {
                span {
                    cursor: pointer;
                }
                span:not(:last-child) {
                    border-right: 1px solid $border_color;
                    padding-right: 3px;
                }
            }
            .active {
                color: $footer_color;
            }
        }
        .redLabelColor {
            color: $red_label_color;
        }
    }
    .page2 #pdfModal .modal-dialog .twoPages {
        min-height: 830px !important;
        .product-body {
            .page-body .product-image {
                min-height: 232px;
            }
            .product-box {
                .product-detail {
                    line-height: 24px;
                }
                .product-rrp {
                    line-height: 20px;
                }
            }
            .preview .preview-background {
                height: 760px;
            }
        }
    }
    .twoPages {
        min-height: 865px !important;
        .product-body .preview .preview-background {
            height: 780px;
        }
    }
    .fourPages {
        min-height: 1070px !important;
        .product-body {
            min-height: 1020px !important;
            .page-body .product-image img {
                max-height: 50%;
            }
        }
        .product-body .preview .preview-background {
            height: 995px;
        }
    }
</style>
