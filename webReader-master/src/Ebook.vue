<template>
    
    <div class="ebook">
        <title-bar
    :ifTitleAndMenuShow='ifTitleAndMenuShow'></title-bar>
        <div class="ebook-wrapper">
            <div id="read"></div>
            <div class="mask">
                <div class="left" @click="prevPage"></div>
                <div class="center" @click="toggleMenuNTitle"></div>
                <div class="right" @click="nextPage"> </div>  
            </div>
        </div> 
        <menu-bar
    :ifTitleAndMenuShow='ifTitleAndMenuShow' 
    :fontSizeList="fontSizeList"
    :defaultFontSize="defaultFontSize" 
    @setFontSize="setFontSize"
    :themeList="themeList"
    :defaultTheme="defaultTheme"
    @setTheme="setTheme"
    :bookAvailable="bookAvailable"
    @onProgressChange="onProgressChange"
    :navigation="navigation"
    @jumpTo="jumpTo"
    ref="menuBar"></menu-bar>
    </div>
    
</template>

<script>
    import Epub from 'epubjs'
    import TitleBar from '@/components/TitleBar'
    import MenuBar from '@/components/MenuBar'
    const DOWNLOAD_URL = 'static/2013_Book_AgriculturalImplicationsOfTheF.epub';

    export default {
        components: {
            TitleBar,
            MenuBar
        },
        data() {
            return{
                ifTitleAndMenuShow: false,
                fontSizeList:[
                    {fontSize:12},
                    {fontSize:14 },
                    {fontSize:16},
                    {fontSize:18},
                    {fontSize:20},
                    {fontSize:22},
                    {fontSize:24}
                ],
                defaultFontSize: 16,
                themeList:[
                    {
                        name: 'default',
                        style: {
                            body: {
                                'color': '#000',
                                'background': '#fff'    
                            }
                        }
                    },
                    {
                        name: 'eye',
                        style: {
                            body: {
                                'color': '#000',
                                'background': '#ceeaba'    
                            }
                        }
                    },
                    {
                        name: 'night',
                        style: {
                            body: {
                                'color': '#fff',
                                'background': '#000'    
                            }
                        }
                    },
                    {
                        name: 'gold',
                        style: {
                            body: {
                                'color': '#000',
                                'background': 'rgb(241, 236, 226 )'    
                            }
                        }
                    } 
                ],
                defaultTheme: 0,
                bookAvailable: false,
                navigation: {}
            }   
        },
        methods: {
            //jump to href
            jumpTo(href) {
                this.rendition.display(href);
                this.hideTtleAndMenu(); 
            },
            hideTtleAndMenu() {
                //hide titleBar and menuBar
                this.ifTitleAndMenuShow = false;
                //hide setting
                this.$refs.menuBar.hideSetting();
                //hide content
                this.$refs.menuBar.hideContent();
            },
            //progress : 0-100
            onProgressChange(progress) {
                const percentage = progress / 100;
                const location = percentage > 0 ? this.locations.cfiFromPercentage(percentage) : 0;
                this.rendition.display(location);

            },
            setTheme(index) {
                this.themes.select(this.themeList[index].name)
                this.defaultTheme = index;
            },
            registerTheme() {
                this.themeList.forEach(theme => {
                    this.themes.register(theme.name, theme.style)
                })
            },
            setFontSize(fontSize) {   
                this.defaultFontSize = fontSize;
                if(this.themes) {
                    this.themes.fontSize(fontSize+'px');
                }
            },
            toggleMenuNTitle() {
                this.ifTitleAndMenuShow = !this.ifTitleAndMenuShow;
                if(!this.ifTitleAndMenuShow){
                    // kind of DOM selector
                    this.$refs.menuBar.hideSetting();
                }
            },
            prevPage() {
                //this.rendition.prev()
                if(this.rendition) {
                    this.rendition.prev();
                }
            },
            nextPage() {
                //this.rendition.prev()
                if(this.rendition) {
                    this.rendition.next()
                }
            },
            showEpub() {
                //resolve book
                this.book = new Epub(DOWNLOAD_URL);
                //test if book resolved  
                //console.log(this.book);

                //render to DOM which id is 'read'
                this.rendition = this.book.renderTo('read', {
                    width: window.innerWidth,
                    height: window.innerHeight
                })
                this.rendition.display()

                //get the theme
                this.themes = this.rendition.themes;
                
                this.setFontSize(this.defaultFontSize);

                //this.themes.rigister(name, styles)
                //this.themes.select(name)
                this.registerTheme();
                this.setTheme(this.defaultTheme);
                //get locations
                //console.log(this.book.locations);
                this.book.ready.then(() => {
                    this.navigation = this.book.navigation;
                    //test if navigation works well
                    //console.log(this.navigation);
                    //href in toc is what we want
                    return this.book.locations.generate()
                }).then(result => {
                    //console.log(result); 
                    this.locations = this.book.locations;
                    //test if onProgressChange is working:
                    //this.onProgressChange(98);
                    this.bookAvailable = true;
                })
            }
        },
        mounted() {
            this.showEpub();
        }
    }
</script>

<style lang='scss' scoped>
    @import 'assets/styles/global';
    .ebook{
        position: relative;
        .ebook-wrapper{
            .mask {
                position: absolute;
                top: 0;
                left: 0;
                z-index: 100;
                display: flex;
                width: 100%;
                height: 100%;
                .left{
                    flex: 0 0 px2rem(100);
                }
                .center{
                    flex: 1;
                }
                .right{
                    flex: 0 0 px2rem(100);
                }

            }
        }
    }
</style>