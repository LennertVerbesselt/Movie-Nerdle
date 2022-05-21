<template lang="eng">
    <div class="actors-container">
        <div class="actor">
            <div class="actor-image">
                <img  :src="'https://image.tmdb.org/t/p/w500/' + this.Actor1.profile_path" alt="" class="item-cover-image">
            </div>
            <span>{{this.Actor1.name}}</span>
        </div>
        <div class="actor">
            <div class="actor-image">
                <img :src="'https://image.tmdb.org/t/p/w500/' + this.Actor2.profile_path" alt="" class="item-cover-image">
            </div>
            <span>{{this.Actor2.name}}</span>
        </div>
        <div class="actor">
            
            <div  class="actor-image">
                <img v-if="this.Guess < 1" src="../assets/Question_Mark.png" alt="" class="item-cover-image">
                <img v-if="this.Guess >= 1" :src="'https://image.tmdb.org/t/p/w500/' + this.Actor3.profile_path" alt="" class="item-cover-image">
                
            </div>
            <span v-if="this.Guess >= 1" >{{this.Actor3.name}}</span>
        </div>
        <div class="actor">
            <div class="actor-image">
                <img v-if="this.Guess < 2" src="../assets/Question_Mark.png" alt="" class="item-cover-image">
                <img v-if="this.Guess >= 2" :src="'https://image.tmdb.org/t/p/w500/' + this.Actor4.profile_path" alt="" class="item-cover-image">
                
            </div>
            <span v-if="this.Guess >= 2" >{{this.Actor4.name}}</span>
        </div>
        <div class="actor">
            <div class="actor-image">
                <img v-if="this.Guess < 3" src="../assets/Question_Mark.png" alt="" class="item-cover-image">
                <img v-if="this.Guess >= 3" :src="'https://image.tmdb.org/t/p/w500/' + this.Actor5.profile_path" alt="" class="item-cover-image">
                
            </div>
            <span v-if="this.Guess >= 3">{{this.Actor5.name}}</span>
        </div>
        <div class="actor">
            <div class="actor-image">
                <img v-if="this.Guess < 4" src="../assets/Question_Mark.png" alt="" class="item-cover-image">
                <img v-if="this.Guess >= 4" :src="'https://image.tmdb.org/t/p/w500/' + this.Actor6.profile_path" alt="" class="item-cover-image">
            </div>
            <span v-if="this.Guess >= 4">{{this.Actor6.name}}</span>
        </div>
    </div>
</template>

<script>
export default {
    name: 'ActorImages',
    props: {
        Actor1: Object,
        Actor2: Object,
        Actor3: Object,
        Actor4: Object,
        Actor5: Object,
        Actor6: Object,
        Guess: Number,
    }
    
}
</script>

<style scoped>
    .actors-container{
        padding-top: 2rem;
        padding-bottom: 0rem;
        display: flex;
        justify-content: center;
        align: center;
        flex-direction: row;
        flex-wrap: wrap;
        width: 90vw;
        max-width: 600px;
        margin: auto;
    }

    .actor {
        width: 28%;
        margin: 0;
        padding: .5rem;
        display: flex;
        flex-direction: column;
        justify-content: space-between;
        align-items: center;

        font-family: Helvetica, Arial, sans-serif;
        text-align: center;

        margin-bottom: 1rem;

        

    }

    .actor-image {
        
        //width: 100%;
        //height: 214px;
        //max-height: 215px;
        margin-bottom: 1.2rem;
        border-radius: 6px;

        background-color: #121213;
        /* box-shadow: 2px 2px 4px #000000, -2px -2px 10px rgba(58, 58, 60, 0.5); */

        /* display: flex;
        justify-content: center;
        align-items: center; */

        
    }

    .actor-image img {
        //height: 100%;
        //height: 210px;
        //border-radius: 6px;
        //margin-bottom: .5rem;
    }

    /* .question-mark {
        font-family: Helvetica, Arial, sans-serif;
        font-weight: 700;
        font-size: 140px;
        color: #121213;

        background-color: #121213;
        color:transparent;
        text-shadow: 0px 0px 0px rgba(58,58,60,.1);
        -webkit-background-clip: text;
            -moz-background-clip: text;
                background-clip: text;
    } */

    .actor img {
        width: auto;
        height: 9rem;
        border-radius: 6px;
        
    }
</style>