<template>
    <div class="home">
        <el-row class="row">
            <el-col :span="6">
                <div class="filter grid-content bg-purple" v-on:click="sortByTitle()">
                    Title
                    <i class="el-icon-arrow-down" v-if="!iconFilterTitle"></i>
                    <i class="el-icon-arrow-up" v-if="iconFilterTitle"></i>
                </div>
            </el-col>
            <el-col :span="10">
                <div class="filter grid-content bg-purple-light" v-on:click="sortByUrl()">
                    URL
                    <i class="el-icon-arrow-down" v-if="!iconFilterUrl"></i>
                    <i class="el-icon-arrow-up" v-if="iconFilterUrl"></i>
                </div>
            </el-col>
            <el-col :span="4">
                <div class="filter grid-content bg-purple" v-on:click="sortByCreatedDate()">
                    Created
                    <i class="el-icon-arrow-down" v-if="!iconFilterCreated"></i>
                    <i class="el-icon-arrow-up" v-if="iconFilterCreated"></i>
                </div>
            </el-col>
            <el-col :span="4">
                <div class="filter grid-content bg-purple-light" v-on:click="sortByNames()">
                    Author
                    <i class="el-icon-arrow-down" v-if="!iconFilterAuthor"></i>
                    <i class="el-icon-arrow-up" v-if="iconFilterAuthor"></i>
                </div>
            </el-col>
        </el-row>
        <el-row v-for="(item, i) in response[page]" :key="i" class="row" >
            <div v-on:click="showModal(item)" class="modal-items">
                <el-col :span="6">
                <div class="grid-content bg-purple" >
                    {{item.title}}
                </div>
            </el-col>
            <el-col :span="10">
                <div class="grid-content bg-purple-light">
                    {{item.url}}
                </div>
            </el-col>
            <el-col :span="4">
                <div class="grid-content bg-purple">
                    {{item.created_at}}
                </div>
            </el-col>
            <el-col :span="4">
                <div class="grid-content bg-purple-light">
                    {{item.author}}
                </div>
            </el-col>
            </div>
        </el-row>
        <div class="modal" v-if="isShowed">
            <p class="modal_text">{{this.modal}}</p>
            <div class="modal_icon">
                <i class="el-icon-close" v-on:click="closeModal()"></i>
            </div>
        </div>
        <div>
            <el-row>
                <el-button 
                v-for="(pag,i) in response"
                :key="i"
                v-on:click="changePagination(i)"
                v-bind:type="page===i?'primary':''">
                    {{i+1}}
                </el-button>
            </el-row>       
        </div>
    </div>
</template>

<script>
    // @ is an alias to /src
    import axios from 'axios';
    import {chunk,debounce} from 'lodash';

    export default {
        name: 'home',
        data() {
            return {
                response: false,
                page: 0,
                iconFilterAuthor: false,
                iconFilterUrl: false,
                iconFilterCreated: false,
                iconFilterTitle: false,
                modal: null,
                isShowed: false,

                
            };
        },
        created() {
            this.getNewPosts();
            this.getData();

        },
        methods: {
            sortByNames(){
                this.iconFilterAuthor = !this.iconFilterAuthor;
                if(!this.iconFilter){
                    this.response[this.page].sort((a, b) => a.author.localeCompare(b.author))
                } else {
                    this.response[this.page].sort((a, b) => b.author.localeCompare(a.author))
                }
                
            },
            sortByUrl(){
                this.iconFilterUrl = !this.iconFilterUrl;
                if(!this.iconFilter){
                    this.response[this.page].sort((a, b) => a.url.localeCompare(b.url))
                } else {
                    this.response[this.page].sort((a, b) => b.url.localeCompare(a.url))
                }
            },
            sortByCreatedDate(){
                this.iconFilterCreated = !this.iconFilterCreated;
                if(!this.iconFilter){
                    this.response[this.page].sort((a, b) => a.created_at.localeCompare(b.created_at))
                } else {
                    this.response[this.page].sort((a, b) => b.created_at.localeCompare(a.created_at))
                }
            },
            sortByTitle(){
                this.iconFilterTitle = !this.iconFilterTitle;
                if(!this.iconFilter){
                    this.response[this.page].sort((a, b) => a.title.localeCompare(b.title))
                } else {
                    this.response[this.page].sort((a, b) => b.title.localeCompare(a.title))
                }
            },
            getNewPosts(){
                setInterval(()=>{
                    this.getData()
                    },
                    10000)
            },
            changePagination(page){
              this.page = page
            },
            getData() {
                axios.get('https://hn.algolia.com/api/v1/search_by_date?tags=story')
                .then((resp) => this.response = chunk(resp.data.hits,10))
                .catch((err) => console.warn('some shit happens'));
            },
            showModal(item) {
                this.modal = item;
                this.isShowed = true;
            },
            closeModal() {
                this.isShowed = false;
                this.modal = null;
            }
        }
    };
</script>
<style lang="scss">
 .modal-items {
     cursor: pointer;
 }

 .row {
     text-align-last: left;
 }
 .modal {
     position: fixed;
     width: 80%;
     height: 30%;
     margin: 0 auto;
     background-color: black;
     top: 50%;
     left: 50%;
     transform: translate(-50%,-50%);
     display: flex;
     justify-content: space-between;
     border-radius: 6px;
 }
 
 .modal_text {
     width: 85%;
     padding: 15px;
     color: #fff;
     height: 100%;
 }
 
 .grid-content {
     overflow: hidden;
 }

 .modal_icon {
     cursor: pointer;
     color: #ffffff;
     font-size: 30px;
 }
 .filter {
     cursor: pointer;
 }
 .el-row {
    margin-bottom: 20px;
    &:last-child {
      margin-bottom: 0;
    }
  }
  .el-col {
    border-radius: 4px;
  }
  .bg-purple-dark {
    background: #ffffff;
  }
  .bg-purple {
    background: #ffffff;
  }
  .bg-purple-light {
    background: #ffffff;
  }
  .grid-content {
    border-radius: 4px;
    min-height: 36px;
  }
  .row-bg {
    padding: 10px 0;
    background-color: #f9fafc;
  }
</style>
