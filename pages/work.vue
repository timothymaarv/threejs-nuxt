<template>
 <div class="bg">
 <h1 class="opacity" ref="work">My Work </h1>
 <div class="imageWrapper"> 
        <div class="container opacity" ref='projects' v-for="project in projects">
        <img :src="project.link" alt="tech">
        <p>{{project.name}}</p>
        </div>
    </div>
    </div>
</template>

<script>
import one from '~/static/one.jpg'
import two from '~/static/two.jpg'
import three from '~/static/three.jpg'
import four from '~/static/four.jpg'
import five from '~/static/five.jpg'
import gsap from 'gsap'

export default {


        data () {
            return {
                projects: [
                    {
                        name: "iphone",
                        link: one
                    },
                    {
                        name: "macbook",
                        link: two
                    },
                    {
                        name: "samsung",
                        link: three
                    },
                    {
                        name: "meta",
                        link: four
                    },
                    {
                        name: "amazon",
                        link: five
                    },
                ]
            }
        },
mounted(){

        gsap.to(this.$refs.work, {
        opacity: 1,
        duration: 1,
        ease: "expo",
        translateY: 0
        })

        gsap.to(this.$refs.projects, {
        opacity: 1,
        duration: 2,
        delay: 0.2,
        stagger: 0.1,
        ease: "expo",
        translateX: 0
        })
}
    }
</script>

<style>
.bg{
 background-color: black;
 color: white; 
 height: 100vh;
 text-transform: uppercase;
}

.opacity{
    opacity: 0;
}

h1{
 font-size: 40px;
 font-weight: bold;
 padding: 40px 24px;
 font-family: "Exo";
 font-style: italic;
 transform: translateY(30px);
}

.imageWrapper{
    display: flex;
    gap: 16px;
    padding: 24px;
   
}

.container{
    display: grid;
    align-items: center;
    text-align: center;
    width: 100%;
    transform: translateX(30px)
}

.container p {
    font-family: "Space mono";
    margin-top: 5px;
}

img{
    height: 50vh;
    object-fit: cover;
    /* width: 100%; */
}
/* @media screen and (max-width: 700px) {
    
} */
</style>