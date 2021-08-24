<template>
   <div class="shorten-box">
            <div class="shorten-input">
                <input type="text" v-model="url" @focus="clearText">
                <span class="text-danger">{{ errorMessage }}</span>
            </div>
            <button class="btn-shorten" @click="getShortenUrl">Shorten</button>
            <div class="shorten-output">
                <p> Link shorten: <span id="foo">{{ shortenUrl }} </span></p>
                <button class="btn-copy" :class="{active: isActive}" data-clipboard-target="#foo">Copy</button>
            </div>
   </div>
</template>

<script>

import ClipboardJS from 'clipboard';

export default {
    name: 'ShortenUrl',

    created() {
        fetch('https://api-ssl.bitly.com/v4/groups', {
            method: "GET",
            headers: {
                "Authorization": `Bearer ${this.token}`,
                "Content-Type": "application/json" 
            }
        })
        .then(response => response.json())
        .then(data => this.groupID = data.groups[0].guid);

        new ClipboardJS('.btn-copy');
    },
    data() {
        return {
            url: '',
            shortenUrl: '',
            token: '93ab3639fc03ab3a9a4a709001d7e19959120dbf',
            groupID: '',
            errorMessage: '',
            historyUrl: [],
            isActive: false,
        }
    },
    methods: {
        clearText() {
            this.url = '';
            this.errorMessage = '';
        },
        getShortenUrl() {
            if(this.url.length === 0) {
               return;
            }

            const options = {
                method: "POST",
                headers: { 
                    "Authorization": `Bearer ${this.token}`,
                    "Content-Type": "application/json" 
                },
                body: JSON.stringify({ "long_url": this.url, "domain": "bit.ly", "group_guid": this.groupID })
            }
            fetch('https://api-ssl.bitly.com/v4/shorten', options)
            .then(response => response.json())
            .then(data => {

                if(data.message === "INVALID_ARG_LONG_URL") {
                    this.errorMessage = "Invalid URL to shorten !";
                    return;
                }
                
                const {link, long_url} = data;

                this.historyUrl.push({
                    id: (new Date()).getTime(),
                    link,
                    longUrl: long_url,
                })
                
                this.shortenUrl = link;
                this.isActive = true;
                this.url = '';
            })
            .catch(error => {
                console.error('There was an error!', error);
            });
        }
    }
}
</script>

<style>
    
        .shorten-box {
            max-width: 700px;
            width: 100%;
            margin: 20px auto;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
        }
        .heading {
            color:#2c3e50;
            text-align: center;
            font-weight: 500;
            font-size: 40px;
            margin-bottom: 30px;
        }

        .shorten-box .shorten-input input {
            padding: 13px 20px;
            outline: none;
            border-color: #2c3e50;
            font-family: 'Rubik', sans-serif;
            font-size: 14px;
        
        }
        .shorten-box .shorten-input span,
        .shorten-box .shorten-input input {
            width: 60%;
            margin: 5px auto;
            display: block;
        }

        .shorten-input {
            width: 100%;
            margin-bottom: 30px;
        }
        button.btn-shorten {
            font-family: 'Rubik', sans-serif;
            background: #333;
            border: none;
            color: #fff;
            font-size: 1.3rem;
            padding: 10px 20px;
            border-radius: 4px;
            cursor: pointer;
            transition: all .3s;

        }
        button:hover {
            opacity: .8;
        }
        span.text-danger {
            color: rgb(221, 62, 62);
            font-size: 15px;
            font-weight: 400;
        }

        .shorten-output {
            display: flex;
            align-items: center;
        }
        .shorten-output .btn-copy {
            font-family: 'Rubik', sans-serif;
            outline: none;
            border: 0;
            border-radius: 3px;
            background-color: #2ecc71;
            margin-left: 10px;
            color: #fff;
            padding: 4px 10px;
            cursor: pointer;
            display: none;

        }
        .shorten-output .btn-copy.active {
            display: block;
        }
</style>