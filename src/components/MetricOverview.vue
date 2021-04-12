<template>
    <div metrics-overview-container>
        <div class="title-container">{{ title }}</div>
        <div class="metric-bottom">
            <div class="value-container">{{ ( fin ? formatMoney(value) : ( percent ? formatPercent(value) :  value)) }}</div>
            <div class="graphic-container">
                <div v-if="!graph" :class="( gain ? 'arrow-up' : 'arrow-down' )" class="graphic arrow"></div>
                <div v-if="graph" class="graphic graph"></div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: 'MetricOverview',
    data() {
        return {

        }
    },
    props: {
        title: {
            type: String,
            default: 'Metric Title'
        },
        value: {
            type: Number,
            default: 25000
        },
        graph: {
            type: Boolean,
            default: false
        },
        fin: {
            type: Boolean,
            default: true
        },
        percent: {
            type: Boolean,
            default: false
        }
    },
    computed: {
        gain() {
            return true; // TODO : Calculate gain based on income
        }
    },
    mounted() {

    },
    methods: {
        formatMoney(value) {
            var formatter = new Intl.NumberFormat('en-US', {
                style: 'currency',
                currency: 'USD',
            });

            return formatter.format(value);
        },
        formatPercent(value) {
            return value + '%'
        }
    }
}
</script>

<style lang="scss" scoped>
.metrics-overview-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    height: 100%;
}

.title-container {
    opacity: 0.3;
    text-transform: uppercase;
    font-weight: bold;
    font-size: 12px;
    margin-bottom: 6px;
}

.metric-bottom {
    display: flex;
    align-items: center;
}

.arrow {
    background-repeat: no-repeat;
    background-size: contain;
    background-position: center;
    height: 12px;
    width: 12px;
    margin-left: 12px;
}

.arrow-up {
    background-image: url('../assets/icons/arrow-up.svg');
}

.arrow-down {
    background-image: url('../assets/icons/arrow-down.svg');
}

.value-container {
    font-weight: bold;
    font-size: 20px;
    width: max-content;
    margin: 0px !important;
}

</style>