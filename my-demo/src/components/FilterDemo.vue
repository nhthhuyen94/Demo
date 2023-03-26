<template>
    <v-container>
        <v-btn @click="onClickOpenFilter" depressed outlined>
            <span v-if="countFilter >0" class="filter-counter d-flex justify-center align-center mr-2"> {{ countFilter }} </span>
            Filters
        </v-btn>
        <!-- <v-layout v-if="isOpenFilter"> -->
            <v-row wrap v-if="isOpenFilter" class="mt-2">
                <v-col md="3" class="">
                    <div class="filter__title d-flex justify-space-between mb-2">
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
                    >
                        <template #selection="{ item }">
                            <span v-if="item.name.length > 20"> {{ item.name.substring(0, 20) }} </span>
                            <span v-else> {{ item.name }} </span>
                        </template>
                        <template #item="{ item }">
                            <span v-if="item.name.length > 20" class="client-name"> {{ item.name.substring(0, 20) + '...'}}  </span>
                            <span v-else> {{ item.name }} </span>

                            <span class="client-number"> {{ item.number }} </span>
                        </template>
                    </v-autocomplete>
                </v-col>
                <v-col md="3">
                    <div class="filter__title d-flex justify-space-between mb-2">
                        <span>Date Range</span>
                        <span v-if="fromData.rangeDate" @click="clearFilter(2)">Clear</span>
                    </div>
                    <v-autocomplete
                        v-model="fromData.rangeDate"
                        :items="listRageDate"
                        :item-text="selectedDateRange? selectedDateRange : textRangeDate"
                        item-value="id"
                        placeholder="All"
                        solo
                        return-object
                        @change="checkCountFilter"
                    >
                        <template #item="{ item }">
                            <v-radio-group v-model="selectedItemDateRange">
                                <div @click="onClickDateRange(item)">
                                    <v-radio :value="item.text" color="#00bcc3" :label="item.text" :key="item.id" @change="checkDateRange(item)">
                                    </v-radio>
                                </div>
                            </v-radio-group>
                        </template>
                    </v-autocomplete>
                    <!-- set Date -->
                    <div v-if="isOpenModalDate" class="filter-set-date pa-3">
                        <div class="title d-flex justify-space-between">
                            <span>Custom date range</span>
                            <span @click="isOpenModalDate = false">Back</span>
                        </div>
                        <div class="check-date d-flex justify-space-between pb-3">
                            <v-btn outlined elevated="0" @click="chooseRangeDateFunc">
                                <v-icon size="16" color="#c0bfbf">mdi-calendar-clock</v-icon>
                                <span>{{chooseRangeDate ? chooseRangeDate : 'DD/MM/YYYY' }}</span>
                            </v-btn>
                            <v-btn outlined elevated="0" @click="chooseRangeDateFunc">
                                <v-icon size="16" color="#c0bfbf">mdi-calendar-clock</v-icon>
                                <span>{{chooseRangeDate ? chooseRangeDate : 'DD/MM/YYYY' }}</span>
                            </v-btn>
                        </div>
                        <div class="confirm-date">
                            <v-btn
                                height="40px"
                                depressed
                                class="cancel"
                                @click="cancel"
                            >
                                Cancel
                            </v-btn>
                            <v-btn
                                height="40px"
                                depressed
                                color="#00bcc3"
                                class="ml-2 save"
                                :disabled="!chooseRangeDate"
                                @click="save"
                            >
                                Save
                            </v-btn>
                        </div>
                    </div>
                </v-col>
                <v-col md="3" >
                    <div class="filter__title d-flex justify-space-between mb-2">
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
                    >
                        <template #item="data">
                            <v-list-item-avatar>
                                <img :src="data.item.url">
                            </v-list-item-avatar>
                            <v-list-item-content>
                                <span> {{ data.item.name }} </span>
                            </v-list-item-content>
                        </template>
                    </v-autocomplete>
                    <!--  -->
                    <!-- <v-autocomplete
                        v-model="fromData.reTouch"
                        :items="listReToucher"
                        item-text="name"
                        item-value="id"
                        placeholder="All"
                        solo
                        @change="checkCountFilter"
                        return-object
                    >
                        <template #item="{ item }">
                            <v-row>
                                <v-img :src="item.url" width="30" height="30" class="custom-img"></v-img>
                                <span> {{ item.name }} </span>
                            </v-row>
                            
                        </template>
                    </v-autocomplete> -->
                </v-col>
                <v-col md="3">
                    <div class="filter__title d-flex justify-space-between mb-2">
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
                    >
                        <template #item="{ item }">
                            <div class="filter__status">
                                <span class="filter__status-dot mr-2" :class="item.text"></span>
                                <span> {{ item.text }} </span>
                            </div>
                        </template>
                    </v-autocomplete>
                </v-col>
            </v-row>
        <!-- </v-layout> -->
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
                { id: 1, name: 'nguyen hoang thu huyen', number: 121222 },
                { id: 2, name: 'nguyen hoang thu huyen 2', number: 121228882 },
                { id: 3, name: 'nguyen hoang thu huyen 3', number: 12120022 },
                { id: 4, name: 'nguyen hoang thu huyen 4', number: 12881222 }
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
            },
            checkCountFilter() {
                let count = 0
                let result = Object.assign({}, this.fromData)
                for (let key in result) {
                    if (result[key]) {
                        if (result[key].id !== 7) {
                            count = count + 1
                        } else {
                            // this.customDateRange()
                        }

                    }
                }
                this.countFilter = count
            },
            checkDateRange(item) {
                if (item.id != 7) {
                    this.selectedItemDateRange = item
                    this.isOpenModalDate= false
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
            textRangeDate(item) {
                if (item.id != 7) {
                    return item.text
                } else {
                    return ''
                }
                
            },
            cancel() {
                this.selectedDateRange = null
            },
            save() {
                this.selectedDateRange = new Date().toLocaleDateString('en-GB') + " - " + new Date().toLocaleDateString('en-GB');
                this.isOpenModalDate = false
                // this.textRangeDate
            }
        }
    }
</script>
<style lang="sass">
.filter-counter
    width: 20px
    height: 25px
    color: #fff
    background: #000
    border-radius: 5px
.v-menu__content
    margin-top: 10px
    border-radius: 5px
.filter__title
    span:first-of-type
        font-weight: bold 
        font-size: 14px
        color: black 
    span:nth-child(2) 
        color: #00bcc3
        cursor: pointer
.custom-img, .v-image__image 
    width: 30px
    height: 30px
    border-radius: 50%
.filter__status
    width: 100%
    display: flex 
    align-items: center
    &-dot
        height: 10px
        width: 10px
        border-radius: 50%
        display: inline-block
.QA 
    background-color: #ff751a
.Rejected        
    background-color:  #e60000
.Expedited
    background-color: #ededed
.filter-set-date
    width: 275px
    border-radius: 5px    
    border: 1px solid #ededed
    .title
        span
            font-size: 14px
        span:first-child
            color: #676565
        span:nth-child(2)
            color: #00bcc3
            cursor: pointer
    .check-date
        .v-btn 
            width: calc(100%/2 - 5px)
            .v-btn__content
                span
                    font-size: 12px
                    color: #676565
    .confirm-date
        .cancel
            .v-btn__content
                color: #747272
        .save 
            .v-btn__content
                color: #ffffff
        .v-btn
            width: calc(100%/2 - 5px)
</style>
