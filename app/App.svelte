<script>
    import { onMount } from 'svelte'
    const utilsModule = require('tns-core-modules/utils/utils')
    const appSettings = require('tns-core-modules/application-settings')
    import { showModal } from 'svelte-native'
    import Article from './modals/Article.svelte'

    let countryNumber = 0
    let country = 'us'

    $: {
        country = countryNumber == 0 ? 'us' : 'no'
        getData()
    }
    const api_key = '0f9e5d308c4740aabb28170d7f2ffd28'


    const getData = () => {
        const url = `https://newsapi.org/v2/top-headlines?country=${country}&apiKey=${api_key}`
        fetch(url)
        .then( response => response.json() )
        .then( json => {
            articles = json.articles
        })
        .catch (error => console.log(error))
    }
    const test = 0

    let articles = []

    onMount(() =>{
        if(appSettings.getNumber('default-country')){
            countryNumber = appSettings.getNumber('default-country')
        }
        getData()
    })

    const showArticle = async (article) => {
        await showModal(
            {
                page: Article,
                props: {
                    article: article
                }
            }
        )
    }

    const setDefaultCountry = () => {
        appSettings.setNumber('default-country', countryNumber)
    }
</script>

<page>
    <actionBar title="World News" />
    <stackLayout>
        <button on:tap={setDefaultCountry} text='set default' /> 
        <scrollView>
            <stackLayout class='articles'>
                {#each articles as article}
                    <flexBoxLayout 
                        on:tap={ () => showArticle(article)}
                        class="article"
                        style="background-image:url('{article.urlToImage}')"
                        >
                            <!-- <image class='img-rounded' src='{article.urlToImage}' alt='cover' stretch='aspectFill' /> -->
                            <label textWrap="{true}" class='h2 white text-center' text='{article.title}' />
                    </flexBoxLayout>
                {:else}
                    <activityIndicator busy={true} />
                {/each}
             </stackLayout>
        </scrollView>
    </stackLayout>
</page>

<style>
    page{
        width:100%;
        background: linear-gradient(to bottom, rgb(117, 153, 208), lightgray);
    }
    .article{
        height: 300;
        padding-bottom: 24;
        margin: 12;
        background-size: cover;
        flex-direction: column;
        justify-content: flex-end;
        align-items: center;
        animation: leftIn .4s ease;
        border-radius: 2;
    }
    @keyframes leftIn{
        from{
            transform: translate(-100, 0)
        }
        to{
            transform: translate(0, 0)
        }
    }
    .white{
        color: white;
    }
</style>
