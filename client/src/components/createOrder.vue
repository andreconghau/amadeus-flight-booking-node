<template src="./createOrder.html"></template>


<style lang="scss" src="./style.scss"></style>

<script>
import Swal from 'sweetalert2/dist/sweetalert2.js'

import 'sweetalert2/src/sweetalert2.scss'
import {
    required,
    email,
    minLength,
    maxLength
} from 'vuelidate/lib/validators'
import {
    BadgerAccordion,
    BadgerAccordionItem
} from 'vue-badger-accordion'
import router from '../router.js'
export default {
    name: 'searchPrice',
    data: () => ({
        toggleInfo: false,
        toggleInfo2: false,
        flightConfirmation: "",
        true: true,
        vm: this,
        components: {
            BadgerAccordion,
            BadgerAccordionItem,
        },
        form: {
            firstName: "theo",
            lastName: "vast",
            isModalVisible: false,
            gender: null,
            age: null,
            email: "abc@gmail.com",
        },
        userSaved: false,
        sending: false,
        lastUser: null,
        menuVisible: false,
        selectedCountryDeparture: "",
        countries: [],
        selectedCountryArrival: null,
        localhost: "http://localhost:2800/",
        info: {},
        info2: {},
        info3: {},
        selectedDateDeparture: "2020-05-01",
        selectedDateArrival: "2020-02-27",
        mojsOptions: {
            count: 6,
            radius: {
                15: 100
            },
            origin: '100% 100%',
            degree: 360,
            children: {
                shape: 'polygon',
                fill: ['blue', 'white'],
                isSwirl: true,
                swirlSize: 10,
                swirlFrequency: 2,
                delay: 'stagger(0, 30)'
            }
        },
        selectedTravel: {},
        searchObject: ""
    }),
    validations: {
        form: {
            firstName: {
                required,
                minLength: minLength(3)
            },
            lastName: {
                required,
                minLength: minLength(3)
            },
            age: {
                required,
                maxLength: maxLength(3)
            },
            gender: {
                required
            },
            email: {
                required,
                email
            }
        }
    },
    computed: {

    },

    watch: {
        selectedTravel() {
            return window.console.log(this.selectedTravel);
        },

    },

    methods: {

        autocompleteCity() {

        },
        getSeletedItem() {
            this.selectedCountryDeparture = this.selectedCountryDeparture.iataCode
        },
        getSeletedItem2() {
            this.selectedCountryArrival = this.selectedCountryArrival.iataCode
        },
        getCountriesDeparture(searchTerm) {
            this.countries = new Promise(resolve => {
                window.setTimeout(() => {
                    if (!searchTerm) {
                        resolve(this.countryList)
                    } else {
                        const term = searchTerm.toLowerCase()

                        resolve(this.countryList.filter(({
                            name
                        }) => name.toLowerCase().includes(term)))
                    }
                }, 500)
            })
        },
        getCountriesDeparture2(searchTerm2) {
            this.countries = new Promise(resolve => {
                window.setTimeout(() => {
                    if (!searchTerm2) {
                        resolve(this.countryList)
                    } else {
                        const term = searchTerm2.toLowerCase()

                        resolve(this.countryList.filter(({
                            name
                        }) => name.toLowerCase().includes(term)))
                    }
                }, 500)
            })
        },
        searchCity() {
            this.showLoader(true)
            var urlSend = "keyword=" + this.selectedCountryDeparture


            // var self =this.selectedCountryDeparture
            async function postUrlEncoded() {

                const response = await fetch("http://localhost:2800/citySearch?" + urlSend, {
                    method: 'GET', // *GET, POST, PUT, DELETE, etc.
                    mode: 'cors', // no-cors, *cors, same-origin
                    cache: 'no-cache', // *default, no-cache, reload, force-cache, only-if-cached
                    credentials: 'same-origin', // include, *same-origin, omit
                    headers: {
                        // 'Content-Type': 'application/json'   
                        'Content-Type': 'application/x-www-form-urlencoded',
                    },
                    redirect: 'follow', // manual, *follow, error
                    referrerPolicy: 'no-referrer', // no-referrer, *client
                    // params: {keyword: self}// body data type must match "Content-Type" header
                });
                return await response.json(); // parses JSON response into native JavaScript objects
            }

            postUrlEncoded().then((data) => {
                window.console.log(data)
                this.countryList = data.data;
                this.$store.commit('dataCitySearchMute', data.data)
                this.showLoader(false)

            });
        },
        searchCity2() {
            this.showLoader(true)
            var urlSend = "keyword=" + this.selectedCountryArrival



            async function postUrlEncoded() {

                const response = await fetch("http://localhost:2800/citySearch?" + urlSend, {
                    method: 'GET', // *GET, POST, PUT, DELETE, etc.
                    mode: 'cors', // no-cors, *cors, same-origin
                    cache: 'no-cache', // *default, no-cache, reload, force-cache, only-if-cached
                    credentials: 'same-origin', // include, *same-origin, omit
                    headers: {
                        // 'Content-Type': 'application/json'   
                        'Content-Type': 'application/x-www-form-urlencoded',
                    },
                    redirect: 'follow', // manual, *follow, error
                    referrerPolicy: 'no-referrer', // no-referrer, *client
                    // body: urlSend// body data type must match "Content-Type" header
                });
                return await response.json(); // parses JSON response into native JavaScript objects
            }

            postUrlEncoded().then((data) => {
                this.countryList = data.data;
                this.$store.commit('dataCitySearchArrival', data.data)
                this.showLoader(false)
            })
        },

        show() {

            Swal.fire({
                title: "Insert your email",
                text: 'hello@something.com',
                input: 'text',
                showCancelButton: true,
                closeOnConfirm: false,
                preConfirm: (inputValue) => {
                    var vm = this
                    vm.getFLightPrice(inputValue);
                },
            });

        },

        getFLightPrice() {

            var vm = this;

            function chooseCity(flight) {
                return flight.id === vm.selectedTravel;
            }
            this.searchObject = this.info2.find(chooseCity);
            this.$store.commit('changePricing', this.searchObject);


            var duh = this.searchObject

            //   {
            //   "data": {
            //       "type": "flight-offers-pricing",
            //       "flightOffers": [this.searchObject]
            // }};
            async function postSearchPrice() {


                const response = await fetch(vm.localhost + "flightprice", {


                    method: 'POST', // *GET, POST, PUT, DELETE, etc.
                    mode: 'cors', // no-cors, *cors, same-origin
                    cache: 'no-cache', // *default, no-cache, reload, force-cache, only-if-cached
                    credentials: 'same-origin', // include, *same-origin, omit
                    headers: {
                        'Content-Type': 'application/json'
                    },
                    redirect: 'follow', // manual, *follow, error
                    referrerPolicy: 'no-referrer', // no-referrer, *client
                    body: JSON.stringify(duh) // body data type must match "Content-Type" header
                });
                return await response.json(); // parses JSON response into native JavaScript objects
            }

            postSearchPrice().then((data) => {
                // window.console.log(data)
                this.flightConfirmation = "PriceConfirmed";
                this.info3 = data;
                this.isLoading = true
                this.createOrderAndget()

            });

        },
        createOrderAndget(data) {
            this.showLoader(true)
            var request = this.$store.getters.pricing

            
            // }
            window.console.log(data)

            this.showLoader(true)

            async function postBody() {
                const response = await fetch("http://localhost:2800" + "/flightCreateOrder", {
                    method: 'POST', // *GET, POST, PUT, DELETE, etc.
                    mode: 'cors', // no-cors, *cors, same-origin
                    cache: 'no-cache', // *default, no-cache, reload, force-cache, only-if-cached
                    credentials: 'same-origin', // include, *same-origin, omit
                    headers: {
                        'Content-Type': 'application/json'
                    },
                    redirect: 'follow', // manual, *follow, error
                    referrerPolicy: 'no-referrer', // no-referrer, *client
                    body: JSON.stringify(request) // body data type must match "Content-Type" header
                });
                return await response.json(); // parses JSON response into native JavaScript objects
            }

            postBody().then((data2) => {
                this.info3 = data2
                window.console.log(data2)
                // this.$store.commit('change', data2)

                // router.push('result')
                this.showLoader(false); // JSON data parsed by `response.json()` call
            }).then(function() {

                async function CreateOrder() {

                    const response = await fetch("http://localhost:2800/" + "flightcretaeorderget");
                    return await response.json();
                }

                try {

                    CreateOrder()
                        .then((json) => {
                            this.info3 = json;

                            if (json.errors) {
                                Swal.fire({
                                    title: 'Error!',
                                    text: "Please choose an another flight please",
                                    icon: 'error',
                                    confirmButtonText: 'skip'
                                })

                            } else {
                                window.console.log(json)
                                this.$store.commit('change', json)
                                if (json.data) {
                                    router.push('result')
                                    this.showLoader(false);
                                } else {
                                    alert("coucou")
                                }

                            }

                        })
                } catch (error) {
                    alert(error);
                    Swal.fire({
                        title: 'Error!',
                        text: 'Do you want to continue' + error,
                        icon: 'error',
                        confirmButtonText: 'skip'
                    })
                }
            })

        },

        chooseflight() {
           this.showLoader(true)
            var vm = this;

            function chooseCity(flight) {
                return flight.id === vm.selectedTravel;
            }
            this.searchObject = this.info2.find(chooseCity);
            this.$store.commit('changePricing', this.searchObject);


            var duh = this.searchObject

            async function postBody() {
                const response = await fetch("http://localhost:2800" + "/flightCreateOrder", {
                    method: 'POST', // *GET, POST, PUT, DELETE, etc.
                    mode: 'cors', // no-cors, *cors, same-origin
                    cache: 'no-cache', // *default, no-cache, reload, force-cache, only-if-cached
                    credentials: 'same-origin', // include, *same-origin, omit
                    headers: {
                        'Content-Type': 'application/json'
                    },
                    redirect: 'follow', // manual, *follow, error
                    referrerPolicy: 'no-referrer', // no-referrer, *client
                    body: JSON.stringify(duh) // body data type must match "Content-Type" header
                });
                return await response.json(); // parses JSON response into native JavaScript objects
            }

              async function CreateOrder() {

                const response = await fetch("http://localhost:2800/" + "flightcretaeorderget");
                return await response.json();
            }

            try{
              postBody()  

          

            try {

                      CreateOrder()
                            .then((json) => {
           this.info3=json;

           if(json.length <2
            ) {
            Swal.fire({
        title: 'Error!',
        text: "Please choose an another flight please",
        icon: 'error',
        confirmButtonText: 'skip'
      })

           }
           else{
           window.console.log(json)
           this.$store.commit('change', json)
           router.push('result')
           this.showLoader(false);
            }

        })
      }

        catch(error) {
        alert(error);
           Swal.fire({
        title: 'Error!',
        text: 'Do you want to continue'+error,
        icon: 'error',
        confirmButtonText: 'skip'
      })
           }


            }
            catch(err){alert(err)}

            
},

        letsFly() {
            this.showLoader(true)
            var vm = this;
            this.info2 = "";
            let bodyDate = "departure=" +
                this.selectedDateDeparture +
                "&arrival=" +
                this.selectedDateArrival +
                "&locationDeparture=" +
                this.selectedCountryDeparture +
                "&locationArrival=" +
                this.selectedCountryArrival;

            window.console.log(bodyDate);

            async function postUrlEncoded() {


                const response = await fetch(vm.localhost + "date?" + bodyDate, {
                    method: 'POST', // *GET, POST, PUT, DELETE, etc.
                    mode: 'cors', // no-cors, *cors, same-origin
                    cache: 'no-cache', // *default, no-cache, reload, force-cache, only-if-cached
                    credentials: 'same-origin', // include, *same-origin, omit
                    headers: {
                        // 'Content-Type': 'application/json'   
                        'Content-Type': 'application/x-www-form-urlencoded',
                    },
                    redirect: 'follow', // manual, *follow, error
                    referrerPolicy: 'no-referrer', // no-referrer, *client
                    body: bodyDate // body data type must match "Content-Type" header
                });
                return await response.json(); // parses JSON response into native JavaScript objects
            }

            postUrlEncoded().then((data) => {

                window.console.log(data)
                this.info2 = data.data;
                this.toggleInfo = true;
                this.showLoader(false)

            }).catch(function(error) {
                window.console.error(error);
                Swal.fire({
                    icon: 'error',
                    title: 'Oops...',
                    text: 'Something went wrong!',
                    footer: '<a href>Why do I have this issue?</a>'
                })
            });

        }
    }

}
</script>