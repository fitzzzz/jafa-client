<template>
    <div id="search-food-component">
        <b-container>
            <b-row>
                <b-col cols="12" offset="0" lg="10" offset-lg="1">
                    <h2>Search or compare some food</h2>
                    <b-btn :pressed.sync="comparisonToggle" variant="outline-warning" class="btn-block">Compare</b-btn>
                    <br/>
                    <comparison v-if="needsComparison" :id1="baseSelect.value.id"
                                :id2="comparisonSelect.value.id"></comparison>
                    <b-row>
                        <b-col @sm=comparisonWidth class="mb-4">
                            <food-select @selected="updateBaseSelect"/>
                            <div v-if="baseSelect!=null&&!comparisonToggle">
                                <food-info v-if="baseSelect!=null&&!comparisonToggle" :food="baseSelect.value"/>
                                <comment-bundle :id="baseSelect.value.id" v-if="baseSelect!=null" location="foods/"></comment-bundle>
                            </div>
                            <div v-else-if="baseSelect!=null" >
                                <br/>
                                <FoodInfoCard :id="baseSelect.value.id"/>
                            </div>
                        </b-col>
                        <b-col sm="6" v-if="comparisonToggle">
                            <food-select @selected="updateComparisonSelect"/>
                            <br/>
                            <FoodInfoCard v-if="comparisonSelect!=null" :id="comparisonSelect.value.id"/>
                        </b-col>
                    </b-row>
                    <div class="separator"></div>
                </b-col>
            </b-row>
        </b-container>
    </div>
</template>

<script>
    import FoodSelect from "../sub/FoodSelect";
    import FoodInfo from "../sub/FoodInfo";
    import Comparison from "../sub/Comparison";
    import MinimalFoodInfo from "../sub/MinimalFoodInfo";
    import CommentList from "../sub/CommentList";
    import CommentBundle from "../sub/CommentBundle";
    import FoodInfoCard from "../composite/FoodInfoCard";

    export default {
        name: 'CompareFood',
        components: {FoodInfoCard, CommentBundle, CommentList, MinimalFoodInfo, Comparison, FoodInfo, FoodSelect},
        data() {
            return {
                comparisonToggle: false,
                baseSelect: null,
                comparisonSelect: null,
            }
        },
        methods: {
            updateBaseSelect(val) {
                this.baseSelect = val;
            },
            updateComparisonSelect(val) {
                this.comparisonSelect = val;
            },
        },
        computed: {
            comparisonWidth: function () {
                return this.comparisonToggle ? '6' : '12';
            },
            needsComparison() {
                return this.comparisonToggle && this.baseSelect != null && this.comparisonSelect != null;
            }
        }
    }
</script>

<style scoped>
    .separator {
        display: block;
        height: 20px;
    }
</style>