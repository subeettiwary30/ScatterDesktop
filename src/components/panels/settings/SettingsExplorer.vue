<template>
    <section>

        <section class="action-box top-pad" v-if="explorers">
            <section v-for="blockchain in blockchainsArray">
                <label>{{blockchainName(blockchain.value)}}</label>

                <sel :options="availableExplorers.hasOwnProperty(blockchain.value) ? availableExplorers[blockchain.value] : defaultExplorers[blockchain.value]"
                     :selected="explorers[blockchain.value]"
                     :parser="x => x.name"
                     v-on:changed="x => changedExplorer(blockchain.value, x)" />
            </section>
        </section>

    </section>
</template>

<script>
    import { mapActions, mapGetters, mapState } from 'vuex'
    import * as Actions from '../../../store/constants';
    import {Blockchains, BlockchainsArray, blockchainName} from '../../../models/Blockchains';
    import PluginRepository from '../../../plugins/PluginRepository'
    import ExplorerService from "../../../services/ExplorerService";



    export default {
        data () {return {
            blockchains:Blockchains,
            blockchainsArray:BlockchainsArray,
            availableExplorers:[],
        }},
        computed:{
            ...mapState([
                'scatter'
            ]),
            ...mapGetters([
                'explorers',
            ]),
            defaultExplorers(){
            	const explorers = PluginRepository.defaultExplorers();
	            return Object.keys(explorers).reduce((acc, blockchain) => {
                    acc[blockchain] = [explorers[blockchain]]
                    return acc;
                }, {})
            }
        },
        mounted(){
        	this.setExplorers();
        },
        methods: {
        	async setExplorers(){
        	    this.availableExplorers = await ExplorerService.getExplorers();
            },
            changedExplorer(blockchain, explorer){
                const scatter = this.scatter.clone();
                scatter.settings.explorers[blockchain] = explorer;
                this[Actions.SET_SCATTER](scatter);
            },
            blockchainName(x){ return blockchainName(x); },
            ...mapActions([
                Actions.SET_SCATTER
            ])
        },
    }
</script>

<style scoped lang="scss" rel="stylesheet/scss">
    @import "../../../variables";

    .action-box {
        > section {
            &:not(:first-child){
                margin-top:20px;
            }
        }
    }
</style>