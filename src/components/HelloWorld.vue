<template>
    <div v-if="isModalOpen" class="modal" @click.self="closeModal">
    <div class="modal-content" style="background: #C1EBF1;">
        <div @click="closeModal" style="background: #FFFFFF; padding: 1rem; font-size: 2rem; border-radius: 10px; cursor: pointer;">Menu</div>
        <ul style="background: #FFFFFF; padding: 1rem 0rem; border-radius: 12px;">
        <li v-for="topic in topics" :key="topic" @click="filterByTopic(topic)" style="background: linear-gradient(90deg, #4CD87C 0%, #42D669 63.42%, #3DD55E 100%); padding: 1rem; color: white; font-size: 2rem; ">
            {{ topic }}
        </li>
        </ul>
        <div style="margin-top: 3rem; background: linear-gradient(180deg, #AFF090 0%, #45C330 100%); padding: 1rem; color: white;font-size: 1.5rem; text-align: left">
            <div style="margin-bottom: 1rem; text-align: center;">
                CONTACTS
            </div>
            <div style="margin-bottom: 1rem;">PHONE: +1(234)-23-45-22</div>
            <div style="margin-bottom: 1rem;">ADDRESS: Green st., Yalow 
                park</div>
            <div style="margin-bottom: 1rem;">EMAIL: Yallow@park.info</div>
        </div>
    </div>
    </div>

    <header style="background: #FFFFFFBD; margin-top: 1rem; padding: 1rem;">
        <div class="header" style="display:flex; justify-content: space-between;">
            <button @click="toggleModal" class="" style="border:none; background: none; cursor: pointer;">
                <img src="../assets/burger.png" style="width: 50px; height:50px;">
            </button>
            <div class="" style="background: linear-gradient(90deg, #E5F67C 0%, #ECEF64 33%, #D2E037 66%, #EAEE3A 100%);padding: 1rem 16rem; border-radius: 5%;">
                <div class="" style="background: linear-gradient(90deg, #FFADAD 0%, #FF774C 100%);
            -webkit-background-clip: text;
            color: transparent; font-weight: 700; font-size: larger;">
                    New trips on Fall season! Full details on our Instagram accounts.
                </div>
            </div>
            <button class="" style="background: #7EEFFF; padding: 0.5rem 1rem; border-radius: 50%; border: none;">
                <img src="../assets/img/avatar.png" style="width: 30px; height:30px;">
            </button>
        </div>
    </header>



    <div style="display: flex; flex: center; justify-content: center;">
        <div class="hexagon-div">
            <!-- Header Section -->

            <div class="header" style="text-align: left; margin-top: 10rem;">
                <div class="date">
                    <div class="border-blue" style="box-shadow: 0px 0px 15px #5BB9CD;" >
                        13.09.2024
                    </div>
                </div>
                <div style="display: flex;
        justify-content: space-between;
        align-items: center; margin-top: 2rem;">
                <div class="border-blue" style=" font-size: 2.5rem;">
                    {{selectedTopic}}
                </div>
                <div class="filter" @click="toggleDropdown"  ref="dropdown" style="position: relative;">
                  <div class="dropdown-toggle" :class="{ open: isOpen }">
                    <img width="20" height="20" src="https://img.icons8.com/ios-filled/50/filter--v1.png" alt="filter--v1" />
                    <span style="margin-left: 1rem;">{{ selectedLabel }}</span> &#9660;
                  </div>

                  <ul v-if="isOpen" class="dropdown-menu">
                    <li @click="selectFilter('rating')">Rating</li>
                    <li @click="selectFilter('pubDate')">Publication Date</li>
                  </ul>
                </div>
                <button @click="previousPage" :disabled="currentPage === 1" style="color: white; cursor: pointer; border: none; background: none;">
                  <img style="  transform: rotate(180deg);
    transition: transform 0.5s ease;" width="30" height="30" src="https://img.icons8.com/ios-glyphs/30/arrow-pointing-left.png" alt="back-arrow" />
                  <div>
                      {{ currentPage }} / {{ totalPages }}
                  </div>
              </button>
                <button @click="nextPage" :disabled="currentPage >= totalPages" style="color: white; cursor: pointer; border: none; background: none;">
                    <img width="30" height="30" src="https://img.icons8.com/ios-glyphs/30/arrow.png" alt="arrow"/>
                    <div>
                        {{ currentPage }} / {{ totalPages }}
                    </div>
                </button>
                </div>
            </div>

            <div class="reviews">
                <div  v-for="person in paginatedPersons" :key="person.id"  class="review-card">
                    <div class="review-details">
                        <div style="display: flex; justify-content: space-between;">
                            <div style="background: #FFFFF526; padding: 0.5rem; text-align: start;">
                                <p><strong>{{ person.PersonName }}</strong> </p>
                                <p>{{ formatDateTime(new Date(person.PubDate)) }}</p>
                            </div>
                            <div>
                                <p><strong>Rating</strong> </p>
                                <p class="rating">
                                  <span class="stars" :style="getStarStyle(person.Rating)">
  ★★★★★
</span>
                                </p>
                            </div>
                            <img :src="person.Avatar" :alt="person.PersonName" width="50" height="50">
                        </div>
                        <p style="word-break: break-all;">{{ person.Commentary }}</p>
                        <div style="display: flex; justify-content: end;">
                        <button class="like-button" @click="incrementStars(person)">LIKE</button>
                        </div>
                    </div>
                </div>

            </div>
        </div>
    </div>
</template>

<script>
import _ from 'lodash';
import PersonList from '../objects/PersonList.vue';
import { format, formatDistanceToNowStrict, isToday } from 'date-fns';

export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  data() {
    return {
      persons: [...PersonList.data().persons],
      filteredPersons: [...PersonList.data().persons],
      topics: ['IT', 'AI', 'VR'],
      selectedTopic: 'IT',
      currentPage: 1,
      itemsPerPage: 4,
      sortByRating: true,
      isModalOpen: false,
      isOpen: false,
      selectedFilter: 'rating',
      options: {
        rating: 'Rating',
        pubDate: 'Publication Date'
      }
    };
  },
  computed: {
    totalPages() {
      return Math.ceil(this.filteredPersons.length / this.itemsPerPage);
    },
    paginatedPersons() {
      const startIndex = (this.currentPage - 1) * this.itemsPerPage;
      const endIndex = startIndex + this.itemsPerPage;
      return this.filteredPersons.slice(startIndex, endIndex);
    },
    selectedLabel() {
      return this.options[this.selectedFilter];
    }
  },
  mounted() {
    this.filteredPersons = this.persons.filter(person => person.Topic === 'IT');
    document.addEventListener('click', this.handleClickOutside);
  },
  beforeUnmount() {
    document.removeEventListener('click', this.handleClickOutside);
  },
  methods: {
    filterByRatingOrDate() {
      if (this.selectedFilter == 'pubDate') {
        this.filteredPersons = _.orderBy(this.persons, ['PubDate'], ['desc']);
        
      } else {
        this.filteredPersons = _.orderBy(this.persons, ['Rating'], ['desc']);
      }

    },
        previousPage() {
        if (this.currentPage > 1) {
            this.currentPage--;
        }
    },
    toggleDropdown() {
      this.isOpen = !this.isOpen;
    },
    selectFilter(option) {
      this.selectedFilter = option;
      this.isOpen = false; 
      this.currentPage = 1;
      this.filterByRatingOrDate();
    },
    getStarStyle(rating) {
      let startColor;
      if (rating <= 1) {
        startColor = '#ff0000'; // Red
      } else if (rating <= 2) {
        startColor = '#ff6f00'; // Orange
      } else if (rating <= 3) {
        startColor = '#ffb904'; // Orange-yellow
      } else if (rating <= 4) {
        startColor = '#ffe234'; // Almost yellow
      } else {
        startColor = '#ffff00'; // Full yellow
      }

      const percentage = (rating * 100) / 5; 

      return {
        background: `linear-gradient(90deg, ${startColor} 0%, ${startColor} ${percentage}%, #e4e4e4 ${percentage}%, #e4e4e4 100%)`,
        '-webkit-background-clip': 'text',
        'color': 'transparent',
      };
    },
    handleClickOutside(event) {
      const dropdown = this.$refs.dropdown;
      if (dropdown && !dropdown.contains(event.target)) {
        this.isOpen = false;
      }
    },
    nextPage() {
      if (this.currentPage < this.totalPages) {
        this.currentPage++;
      }
    },
    toggleModal() {
      this.isModalOpen = !this.isModalOpen;
    },
    closeModal() {
      this.isModalOpen = false;
    },
    filterByTopic(topic) {
      this.filteredPersons = this.persons.filter(person => person.Topic === topic);
      this.selectedTopic = topic;
      this.currentPage = 1;
      this.closeModal();
    },
    formatDateTime(date) {
      if (isToday(date)) {
        return `Today, ${format(date, 'HH:mm')}`;
      } else {
        return `${formatDistanceToNowStrict(date, { addSuffix: true })}, ${format(date, 'HH:mm')}`;
      }
    },
    stars(rating) {
      const fullStars = Math.floor(rating);
      const halfStar = rating % 1 !== 0 ? 1 : 0;
      const emptyStars = 5 - fullStars - halfStar;

      return '⭐'.repeat(fullStars) + (halfStar ? '⭐½' : '') + '☆'.repeat(emptyStars);
    },
    incrementStars(person) {
      if(person.Rating < 4.9) {
        person.Rating += 0.1;

            this.filterByRatingOrDate();
      }
    },
  }
}
</script>

<style>
body {
    font-family: Arial, sans-serif;
    background: url('../assets/img/background.png') no-repeat center center fixed; 
    background-size: cover;
    margin: 0;
    padding: 0;
}
</style>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

.container {
    background-color: white;
    border-radius: 15px;
    width: 80%;
    max-width: 1200px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}
.date {
    font-size: 2em;
    color: #4b9cd3;
}
.filter {
    background: #EEFCF7;
    font-size: 2rem;
    font-weight: 900;
    color: #a6e168;
    cursor: pointer;
    padding: 0rem 1rem;
}

.dropdown-toggle {
  background: #EEFCF7;
  font-size: 2rem;
  font-weight: 900;
  color: #a6e168;
  padding: 0rem 1rem;
  display: flex;
  align-items: center;
  border: none;
  border-radius: 0.5rem;
}

.dropdown-toggle.open {
  background-color: #d1f2eb; 
}

.dropdown-menu {
  position: absolute;
  top: 3rem; 
  left: 0;
  background: #fff;
  border: 1px solid #ccc;
  border-radius: 0.5rem;
  width: 100%;
  list-style: none;
  padding: 0;
  margin: 0;
  box-shadow: 0px 4px 6px rgba(0, 0, 0, 0.1);
}

.dropdown-menu li {
  padding: 1rem;
  font-size: 1.8rem;
  font-weight: 900;
  color: #555;
  cursor: pointer;
  background-color: #EEFCF7;
}

.dropdown-menu li:hover {
  background-color: #d1f2eb;
  color: #333;
}
.reviews {
    display: flex;
    flex-wrap: wrap;
    justify-content: space-between;
}
.review-card {
    background: #5BB9CD;
    border-radius: 10px;
    width: 45%;
    margin: 10px 0;
    padding: 20px;
    box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
    display: flex;
    align-items: center;
    color: white;
}
.review-card img {
    border-radius: 50%;
    margin-right: 20px;
}
.review-details {
    flex-grow: 1;
}
.review-details p {
    margin: 5px 0;
}
.rating {
    color: #ffcc00;
}
.stars {
  font-size: 1.5em;
    letter-spacing: 6px;
    background: -webkit-gradient(linear, left top, right top, color-stop(0, #ffb904), color-stop(55%, #ffb904), color-stop(0, #e4e4e4), to(#e4e4e4));
    background: linear-gradient(90deg, #ffb904 0, #ffb904 55%, #e4e4e4 0, #e4e4e4 100%);
    -webkit-background-clip: text;
    color: transparent;
    line-height: 1;
    -webkit-user-select: none;
    -moz-user-select: none;
    -ms-user-select: none;
    user-select: none;
}
.like-button {
    background-color: #a6e168;
    padding: 10px;
    border: none;
    border-radius: 10px;
    color: white;
    cursor: pointer;
}

.hexagon-div {
    width: 1200px;
    height: 1000px;
    background: linear-gradient(180deg, #FEFEFE 0%, #CED2D2 40%, #C1C5C5 62%, #B8BBBB 100%);
    clip-path: polygon(50% 10%, 100% 15%, 100% 85%, 50% 80%, 0% 85%, 0% 15%);
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
    padding: 20px;
}

.border-blue {
    background: #5BB9CD; color: white; padding: 0rem 1rem; width: 200px; border-radius: 8px;
}

.modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  align-items: center;
  z-index: 99999;
}

.modal-content {
  background-color: white;
  padding: 20px;
  width: 300px;
  height: 100vh;
  text-align: center;
}

.modal ul {
  list-style: none;
  padding: 0;
}

.modal ul li {
  cursor: pointer;
  margin: 10px 0;
  color: #007BFF;
}

.modal ul li:hover {
  text-decoration: underline;
}
</style>
