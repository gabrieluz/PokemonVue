<template>
        <div id="container">
            <div class="pokemonCar scrollBar">
                <pokemonCard 
                    :pokemons="starters"
                    :selectedId="selectedId"
                    @pokemonClicked="fetchEvolutions"
                />
            </div>
        
            <div class="pokemonCar fixed">
                <pokemonCard 
                    :pokemons="evolutions"
                />
            </div>
        </div>
</template>

<script>
import Card from './components/card.vue'
import pokemonCard from './components/pokemonCard.vue'

const api = 'https://pokeapi.co/api/v2/pokemon' 

// Quando habilitado aumenta a quantidade de pokemons em tela
/*
const TAM = 60 
const STARTER_IDS = []
for(let i =1; i<TAM;i+=3){
    STARTER_IDS.push(i)
    console.log(STARTER_IDS)
}
*/
const STARTER_IDS = [1,4,7]
export default {
    components:{
        Card,
        pokemonCard
    },
    data(){
        return{
            starters:[],
            evolutions:[],
            selectedId: null
        }
    },
    methods:{
        async fetchEvolutions(pokemon){
            this.evolutions = await this.fetchData([pokemon.id + 1, pokemon.id + 2 ])
            this.selectedId = pokemon.id
        },

        async fetchData(ids){
            const responses = await Promise.all(ids.map(id => window.fetch(`${api}/${id}`)))

            console.log(`${api}/`)
            const data = await Promise.all(responses.map(res => res.json()))
            return data.map(datum =>({
                id: datum.id,
                name: datum.name,
                sprite: datum.sprites.other['official-artwork'].front_default,
                types: datum.types.map(type => type.type.name)
            }))
        }
    },
    async created(){
        const starters = await this.fetchData(STARTER_IDS)
        this.starters = starters
    }
}
</script>

<style scoped>
#container{
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
}
.pokemonCar + .pokemonCar{
    margin-top: 20px;
}
.scrollBar{
    scroll-margin-bottom: initial;
}
.fixed{
    display: flex;
    align-items: center;
    justify-content: center;
}
</style>