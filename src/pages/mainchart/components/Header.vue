<template>
    <b-navbar toggleable="lg" type="light" variant="light">
        <b-navbar-brand href="#/"><span>X</span><span>C</span>HARTS</b-navbar-brand>

        <b-nav-form v-on:submit.prevent="symbolInputEntered">
            <b-form-input v-model="symbolInputText" v-on:keyup.enter="symbolInputEntered" v-uppercase id="symbolInput" size="sm" class="mr-sm-2"></b-form-input>
            <b-button-group>
                <b-button v-on:click="dailyButtonClicked" id="dailyButton" v-bind:variant="dailyButtonVariant" v-bind:class="{chartButtonActive: dailyActive}" size="sm" class="my-2 my-sm-0" type="button">Daily</b-button>
                <b-button v-on:click="weeklyButtonClicked" id="weeklyButton" v-bind:variant="weeklyButtonVariant" v-bind:class="{chartButtonActive: weeklyActive}" size="sm" class="my-2 my-sm-0" type="button">Weekly</b-button>
            </b-button-group>
            <b-button-group>
                <b-button v-on:click="amdcButtonClicked" id="amdcButton" v-bind:variant="amdcButtonVariant" size="sm" class="my-2 my-sm-0 ml-3" type="button">AMDC</b-button>
                <b-tooltip target="amdcButton" variant="secondary" delay="1500">Actionable Market Direction Call</b-tooltip>
                <b-button v-on:click="esmButtonClicked" id="esmButton" v-bind:variant="esmButtonVariant" size="sm" class="my-2 my-sm-0 " type="button">ESM</b-button>
                <b-tooltip target="esmButton" variant="secondary" delay="1500">Earnings, Sales, Margin</b-tooltip>
                <b-button v-on:click="tradesButtonClicked" id="tradesButton" v-bind:variant="tradesButtonVariant" size="sm" class="my-2 my-sm-0 " type="button">Trades</b-button>
                <b-tooltip target="tradesButton" variant="secondary" delay="1500">Trades, Portfolio</b-tooltip>
            </b-button-group>
        </b-nav-form>

        <b-navbar-toggle target="nav-collapse"></b-navbar-toggle>

        <b-collapse id="nav-collapse" is-nav>
            <b-navbar-nav>
                <!-- b-nav-item href="#">Report</b-nav-item -->
            </b-navbar-nav>

            <!-- Right aligned nav items -->
            <b-navbar-nav class="ml-auto">

                <b-nav-item-dropdown right>
                    <template v-slot:button-content>
                        Tools
                    </template>
                    <b-dropdown-item href="#/trades">Trades</b-dropdown-item>
                    <b-dropdown-item href="#/settings">Settings(Page)</b-dropdown-item>
                </b-nav-item-dropdown>

                <b-nav-form>
                    <b-button @click="$bvModal.show('modal-settings')" size="sm" class="my-2 my-sm-2 ml-3" type="button">Settings</b-button>
                    <b-button v-on:click="inspectButtonClicked" id="inspectButton" ref="inspectButton" size="sm" class="my-2 my-sm-2 ml-3" type="button">Inspect</b-button>
                </b-nav-form>

            </b-navbar-nav>
        </b-collapse>
    </b-navbar>
</template>

<script>
    import Vue from "vue";

    export default {
        name: "Header",

        components: {
        },

        data() {
            return {
                symbolInputText: this.$store.state.currentSymbol,
            }
        },

        computed: {
            dailyButtonVariant() {
                return this.$store.state.currentTimeFrame == "daily"? "info" : "outline-info"
            },
            weeklyButtonVariant() {
                return this.$store.state.currentTimeFrame == "weekly"? "info" : "outline-info"
            },
            amdcButtonVariant() {
                return this.$store.state.amdc? "danger" : "outline-danger"
            },
            esmButtonVariant() {
                return this.$store.state.esm? "danger" : "outline-danger"
            },
            tradesButtonVariant() {
                return this.$store.state.trades? "danger" : "outline-danger"
            },
            dailyActive() {
                return this.$store.state.currentTimeFrame == "daily"
            },
            weeklyActive() {
                return this.$store.state.currentTimeFrame == "weekly"
            }
        },

        methods: {
            symbolInputEntered() {
                console.log("==> Header::symbolInputEntered: " + this.symbolInputText)
                this.$xEventBus.$emit('loadChartData', this.symbolInputText)
            },

            dailyButtonClicked() {
                console.log("==> Header::dailyButtonClicked clicked")

                // Update vuex state
                this.$store.commit("updateTimeFrame", "daily")

                // Trigger reload of the chart
                // this.symbolInputText is updated after an input event in the text box. The streamer-sidebar chrome extension
                // uses jquery to update the text box -> $('#symbolInput').val(symbol); This does not trigger an input event,
                // so this.symbolInputText does not get updated. After trying multiple ways to get this sorted out, finally
                // we came to this solution where we just use direct javascript to read the value from the text box
                let symbolInputActualValue = document.getElementById("symbolInput").value;
                console.log("Header::dailyButtonClicked:: symbolInputActualValue = ", symbolInputActualValue, "this.symbolInputText = ", this.symbolInputText)
                if (this.symbolInputText != symbolInputActualValue) {
                    console.log("Header::dailyButtonClicked:: Updating this.symbolInputText")
                    this.symbolInputText = symbolInputActualValue
                    console.log("Header::dailyButtonClicked:: symbolInputActualValue = ", symbolInputActualValue, "this.symbolInputText = ", this.symbolInputText)
                }
                this.$xEventBus.$emit('loadChartData', this.symbolInputText)
            },

            weeklyButtonClicked() {
                console.log("==> Header::weeklyButtonClicked clicked")

                // Update vuex state
                this.$store.commit("updateTimeFrame", "weekly")

                // Trigger reload of the chart
                // See the comment on the dailyButtonClicked() on why we are using direct javascript below insted of vue way of doing things
                let symbolInputActualValue = document.getElementById("symbolInput").value;
                console.log("Header::weeklyButtonClicked:: symbolInputActualValue = ", symbolInputActualValue, "this.symbolInputText = ", this.symbolInputText)
                if (this.symbolInputText != symbolInputActualValue) {
                    console.log("Header::weeklyButtonClicked:: Updating this.symbolInputText")
                    this.symbolInputText = symbolInputActualValue
                    console.log("Header::weeklyButtonClicked:: symbolInputActualValue = ", symbolInputActualValue, "this.symbolInputText = ", this.symbolInputText)
                }
                this.$xEventBus.$emit('loadChartData', this.symbolInputText)
            },

            amdcButtonClicked() {
                console.log("==> Header::amdcButtonClicked clicked")
                this.$store.state.amdc = !this.$store.state.amdc
                this.$xEventBus.$emit('showHideAmdc', this.$store.state.amdc)
            },

            esmButtonClicked() {
                console.log("==> Header::esmButtonClicked clicked")
                this.$store.state.esm = !this.$store.state.esm
                this.$xEventBus.$emit('showHideEsm', this.$store.state.esm)
            },

            tradesButtonClicked() {
                console.log("==> Header::tradesButtonClicked clicked")
                this.$store.state.trades = !this.$store.state.trades
                this.$xEventBus.$emit('showHideTrades', this.$store.state.trades)
            },

            inspectButtonClicked() {
                console.log("==> Header::inspectButtonClicked clicked")
                console.log("this.$store.state.currentSymbol = " + this.$store.state.currentSymbol);
                console.log("this.$store.state.currentCompanyName = " + this.$store.state.currentCompanyName);
                console.log("this.$store.state.amdc = " + this.$store.state.amdc);
                console.log("this.$store.state.esm = " + this.$store.state.esm);
                console.log("this.$store.state.trades = " + this.$store.state.trades);
                console.log("this.$store.state.settings = ", this.$store.state.settings);
                console.log("this.$store.state = ", this.$store.state);
            },

        },
    };

    // Vue Directives

    Vue.directive('uppercase', {
        /*update (el) {
            console.log("vue directive: uppercase")
            el.value = el.value.toUpperCase()
        },*/
        inserted: function(el, _, vnode) {
            el.addEventListener('input', async function(e) {
                console.log("In thei nput")
                e.target.value = e.target.value.toUpperCase()
                vnode.componentInstance.$emit('input', e.target.value.toUpperCase())
            })
        },
    })


    //
    // Some plain old Javascript code for "advanced" features
    //

    // I hate some nuiances of bootrap and this is one of them. After a button is clicked, it keeps the button in focus
    // state and does not have the original color of the button which can look ugly sometimes.
    document.addEventListener('click', function(e) {
        if(document.activeElement.toString() == '[object HTMLButtonElement]') {
            document.activeElement.blur();
        }
        console.log(e);  // To avoid getting unused vars error during buid time
    });

</script>

<style scoped>
    *:focus,
    *:active
    {
        outline: none;
        box-shadow: none !important;
        -webkit-appearance: none;
    }

    .btn:focus {
        outline: none;
    }

    .button:focus {
        outline: none;
    }

    .navbar-brand {
    //font-variant:small-caps;
    //font-weight:bold;
    }
    .navbar-brand span {
        font-size: 130%;
    }
    .navbar-light .navbar-brand {
        color: #1184ff;
    }

</style>
