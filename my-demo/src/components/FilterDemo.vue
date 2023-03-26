<template>
    <v-container class="filter-demo">
        <v-btn
            height="42"
            width="102"
            @click="onClickOpenFilter"
            depressed
            outlined
            class="filter-demo__btn"
        >
            <span v-if="countFilter >0" class="filter-counter d-flex justify-center align-center mr-2"> {{ countFilter }} </span>
            <span style="color: #1F1F1F; text-transform: capitalize;">Filters</span>
        </v-btn>
        <v-row wrap v-if="isOpenFilter" class="mt-2">
            <v-col md="3" class="">
                <div class="filter__title d-flex justify-space-between mb-3">
                    <span class="">Client</span>
                    <span v-if="fromData.client" @click="clearFilter(1)">Clear</span>
                </div>
                <v-autocomplete
                    v-model="fromData.client"
                    :items="listClitent"
                    item-text="name"
                    item-value="id"
                    placeholder="All"
                    @change="checkCountFilter"
                    solo
                    depressed
                    return-object
                    class="autocomplete-custom"
                >
                    <template #selection="{ item }">
                        <span v-if="item.name.length > 20"> {{ item.name.substring(0, 20) }} </span>
                        <span v-else> {{ item.name }} </span>
                    </template>
                    <template #item="{ item }">
                        <div class="autocomplete-custom__list-item">
                            <span v-if="item.name.length > 20" class="client-name"> {{ item.name.substring(0, 20) + '...'}}  </span>
                            <span v-else> {{ item.name }} </span>
                            <span class="client-number ml-2"> {{ item.number }} </span>
                        </div>
                    </template>
                </v-autocomplete>
            </v-col>
            <v-col md="3">
                <div class="filter__title d-flex justify-space-between mb-3">
                    <span>Date Range</span>
                    <span v-if="fromData.rangeDate" @click="clearFilter(2)">Clear</span>
                </div>
                <v-autocomplete
                    v-model="fromData.rangeDate"
                    :items="listRageDate"
                    item-value="id"
                    placeholder="All"
                    solo
                    return-object
                    @change="checkCountFilter"
                    class="autocomplete-custom"
                >
                    <template #selection="{ item }">
                        <span v-if="item.id != 7">
                            {{ item.text }}
                        </span>
                        <span v-else> {{selectedDateRange}} </span>
                    </template>
                    <template #item="{ item }">
                        <v-radio-group v-model="selectedItemDateRange">
                            <div @click="onClickDateRange(item)">
                                <v-radio :value="item.text" color="#00bcc3" :label="item.text" :key="item.id" class="range-date-text">
                                </v-radio>
                            </div>
                        </v-radio-group>
                    </template>
                </v-autocomplete>
                <!-- set Date -->
                <div v-if="isOpenModalDate" class="filter-set-date">
                    <div class="title d-flex justify-space-between mb-3">
                        <span>Custom date range</span>
                        <span @click="isOpenModalDate = false">Back</span>
                    </div>
                    <div class="check-date d-flex justify-space-between pb-3">
                        <v-btn outlined elevated="0" @click="chooseRangeDateFunc">
                            <v-icon size="16" color="#A9A9A9" class="mr-2" >mdi-calendar-clock</v-icon>
                            <span>{{chooseRangeDate ? chooseRangeDate : 'DD/MM/YYYY' }}</span>
                        </v-btn>
                        <v-btn outlined elevated="0" @click="chooseRangeDateFunc">
                            <v-icon size="16" color="#A9A9A9" class="mr-2">mdi-calendar-clock</v-icon>
                            <span>{{chooseRangeDate ? chooseRangeDate : 'DD/MM/YYYY' }}</span>
                        </v-btn>
                    </div>
                    <div class="confirm-date d-flex justify-space-between mb-3">
                        <v-btn
                            height="32px"
                            depressed
                            class="cancel"
                            @click="cancel"
                        >
                            Cancel
                        </v-btn>
                        <v-btn
                            height="32px"
                            depressed
                            color="#28A3B4"
                            class="save"
                            :disabled="!chooseRangeDate"
                            @click="save"
                        >
                            Save
                        </v-btn>
                    </div>
                </div>
            </v-col>
            <v-col md="3" >
                <div class="filter__title d-flex justify-space-between mb-3">
                    <span>Retoucher</span>
                    <span v-if="fromData.reTouch" @click="clearFilter(3)">Clear</span>
                </div>
                <!--  -->
                <v-autocomplete
                    v-model="fromData.reTouch"
                    :items="listReToucher"
                    item-text="name"
                    item-value="id"
                    placeholder="All"
                    solo
                    @change="checkCountFilter"
                    return-object
                    class="autocomplete-custom"
                >
                    <template #selection="{ item }">
                        <span>
                            {{ item.name }}
                        </span>
                    </template>
                    <template #item="data">
                        <v-list-item-avatar class="retoucher-avatar">
                            <img :src="data.item.url">
                        </v-list-item-avatar>
                        <v-list-item-content class="retoucher-name">
                            <span> {{ data.item.name }} </span>
                        </v-list-item-content>
                    </template>
                </v-autocomplete>
            </v-col>
            <v-col md="3">
                <div class="filter__title d-flex justify-space-between mb-3">
                    <span>Status</span>
                    <span v-if="fromData.status" @click="clearFilter(4)">Clear</span>
                </div>
                <v-autocomplete
                    v-model="fromData.status"
                    :items="listStatus"
                    item-text="text"
                    item-value="id"
                    placeholder="All"
                    solo
                    return-object
                    @change="checkCountFilter"
                    class="autocomplete-custom"
                >
                    <template #selection="{ item }">
                        <span>
                            {{ item.text }}
                        </span>
                    </template>
                    <template #item="{ item }">
                        <div class="filter__status">
                            <span class="filter__status-dot mr-2" :class="item.text"></span>
                            <span class="filter__status-text"> {{ item.text }} </span>
                        </div>
                    </template>
                </v-autocomplete>
            </v-col>
        </v-row>
    </v-container>
</template>

<script>
    export default {
        name: 'FilterDemo',

        data: () => ({
            countFilter: 0,
            isOpenFilter: false,
            fromData: {
                client: null,
                dateRange: null,
                reTouch: null,
                status: null,
            },
            selectedItemDateRange: null,
            isOpenModalDate: false,
            selectedDate: null,
            chooseRangeDate:null,
            selectedDateRange: null,
            listClitent: [
                { id: 1, name: '1 nguyen hoang thu huyen', number: 121222 },
                { id: 2, name: '2 nguyen hoang thu huyen 2', number: 121228882 },
                { id: 3, name: '3 nguyen hoang thu huyen 3', number: 12120022 },
                { id: 4, name: '4 nguyen hoang thu huyen 4', number: 12881222 }
            ],
            listRageDate: [
                { id: 1, text: "Today", isActive: false},
                { id: 2, text: "Yesterday", isActive: false},
                { id: 3, text: "This Week", isActive: false},
                { id: 4, text: "Last Week", isActive: false},
                { id: 5, text: "This Month", isActive: false},
                { id: 6, text: "Last Month", isActive: false},
                { id: 7, text: "Custom", isActive: false},
            ],
            listReToucher: [
                // <img alt="Vue logo" src="./assets/logo.png">
                { id: 1, url: 'https://www.w3schools.com/html/pic_trulli.jpg', name: "Hanna Curtis" },
                { id: 2, url: 'https://www.w3schools.com/html/pic_trulli.jpg', name: "James Vetrovs" },
                { id: 3, url: 'https://www.w3schools.com/html/pic_trulli.jpg', name: "Jakob Kenter" },
                { id: 4, url: 'https://www.w3schools.com/html/pic_trulli.jpg', name: "Livia Korsgaard" },
                { id: 5, url: 'https://www.w3schools.com/html/pic_trulli.jpg', name: "Selena Gomez" },
                { id: 6, url: 'https://www.w3schools.com/html/pic_trulli.jpg', name: "Angelina Jolie" },
                { id: 7, url: 'https://www.w3schools.com/html/pic_trulli.jpg', name: "Adam Levine" },
                { id: 8, url: 'https://www.w3schools.com/html/pic_trulli.jpg', name: "Hanna Curtis" },
            ],
            listStatus: [
                { id: 1, text: "QA", disabled: false },
                { id: 2, text: "Rejected", disabled: false },
                { id: 3, text: "Expedited", disabled: true}
            ]
        }),
        watch: {

        },
        methods: {
            onClickOpenFilter() {
                if (this.isOpenFilter) {
                    this.isOpenFilter = false
                } else {
                    this.isOpenFilter = true
                }
            },
            clearFilter(value) {
                switch (value) {
                    case 1:
                        this.fromData.client = null
                        this.countFilter--
                        break;
                    case 2:
                        this.fromData.rangeDate = null
                        this.isOpenModalDate = false
                        this.countFilter--
                        this.selectedDateRange = null
                        this.selectedItemDateRange = null
                        this.chooseRangeDate = null
                        break;
                    case 3:
                        this.fromData.reTouch = null
                        this.countFilter--
                        break
                    case 4: 
                        this.fromData.status = null
                        this.countFilter--
                        break
                    default:
                        break
                }
                let result = Object.assign({}, this.fromData)
                if (Object.keys(result).length > 0) {
                    for (const [key, value] of Object.entries(result)) {
                    if (value === null || value === '') {
                            delete result[key]
                        }
                    }
                }
                this.$emit('filter-return', result)
            },
            checkCountFilter() {
                let count = 0
                let result = Object.assign({}, this.fromData)
                for (let key in result) {
                    if (result[key]) {
                        if (result[key].id !== 7) {
                            count = count + 1
                        }
                    }
                }
                if (Object.keys(result).length > 0) {
                    for (const [key, value] of Object.entries(result)) {
                    if (value === null || value === '') {
                            delete result[key]
                        }
                    }
                }
                this.countFilter = count
                this.$emit('filter-return', result)
            },
            checkDateRange(item) {
                if (item.id != 7) {
                    this.selectedItemDateRange = item
                    this.isOpenModalDate= false
                } else {
                    this.selectedItemDateRange = null
                }

            },
            chooseRangeDateFunc() {
                this.chooseRangeDate = new Date().toLocaleDateString('en-GB');
            },
            customDateRange() {
                this.fromData.rangeDate = new Date().toLocaleDateString('en-GB');
                this.selectedDate = new Date().toLocaleDateString('en-GB');
                return this.selectedDate
            },
            onClickDateRange(item) {
                if (item.id == 7) {
                    this.isOpenModalDate = true
                }
            },
            cancel() {
                this.selectedDateRange = null
            },
            save() {
                this.selectedDateRange = new Date().toLocaleDateString('en-GB') + " - " + new Date().toLocaleDateString('en-GB');
                this.isOpenModalDate = false
                let result = Object.assign({}, this.fromData)
                let count = 0
                for (let key in result) {
                    if (result[key]) {
                        count++
                    }
                }
                this.countFilter = count
                
            }
        }
    }
</script>
<style lang="sass">
.filter-demo
    .filter-demo__btn
        border: thin solid #5c5c5c
        .filter-counter
            width: 21px
            height: 25px
            color: #fff
            background: #233645
            border-radius: 4px
            font-size: 12px
            font-weight: 700
    .filter__title
        span:first-of-type
            font-weight: 700
            font-size: 16px
            color: #0E161D 
        span:nth-child(2) 
            font-size:14px 
            font-weight: 500 
            color: #28A3B4
            cursor: pointer    
    .autocomplete-custom
        .v-input__slot
            height: 42px
            min-height: 44px
            width: 288px
            border: 1px solid #CACACA
            border-radius: 6px
            box-shadow: none !important
            font-size: 14px
            background:#ffffff
.range-date-text
    .v-label
        font-size: 14px
        font-weight: 700
        color: #0E161D
.client-name, client-number
    font-size: 14px
    font-weight: 700
.client-name
    color: #233645
.client-number
    color: #A9A9A9

.v-list-item
    min-height: 40px
    padding:0 12px
.v-menu__content
    border-radius: 6px !important
    border: 1px solid #CACACA
    box-shadow: 0px 20px 25px rgba(140, 140, 140, 0.25)
    margin-top: 12px
.retoucher-avatar
    height: 20px !important
    width: 20px !important
    min-width: 20px !important
.retoucher-name
    span 
        font-weight: 700
        font-size: 14px
        color: #0E161D
.v-select__slot
    .v-select__selections
        span 
            font-weight: 500
            font-size: 14px
            color: #1F1F1F
/** */

.custom-img, .v-image__image 
    width: 30px
    height: 30px
    border-radius: 50%
.filter__status
    width: 100%
    display: flex 
    align-items: center
    &-dot
        height: 8px
        width: 8px
        border-radius: 50%
        display: inline-block
    &-text
        font-size: 14px
        font-weight: 500
        // color: #0E161D
.QA 
    background-color: #FD853A
.Rejected        
    background-color: #EC4A0A
.Expedited
    background-color: #C1453D
.filter-set-date
    width: 287px
    height: 150px
    border-radius: 6px    
    border: 1px solid #CACACA
    padding: 13px 12px
    .title
        span
            font-weight: 700
        span:first-child
            font-size: 13px
            color: #233645
        span:nth-child(2)
            font-size: 12px
            color: #28A3B4
            cursor: pointer
    .check-date
        .v-btn 
            width: 128px
            height: 32px
            background: #ffffff
            border: 1px solid #CACACA
            border-radius: 6px
            padding: 8px
            .v-btn__content
                span
                    font-size: 12px
                    font-weight: 500
                    color: #757575
    .confirm-date
        .cancel
            background: #ffffff
            .v-btn__content
                color: #1F1F1F
                font-size: 14px
                font-weight:500
                text-transform: capitalize
        .save 
            .v-btn__content
                color: #ffffff
                text-transform: capitalize
        .v-btn
            padding: 8px
            width: 128px
            border: 1px solid #CACACA
            border-radius: 4px
.v-btn::before 
    background-color: transparent

</style>
