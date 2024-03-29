<template>
  <div class="find-doctor-container">
    <div class="search-bar">
      <input type="text" v-model="searchQuery" placeholder="Search by: Physician Name, Specialty" />
      <button @click="searchDoctors">🔍</button>
    </div>

    <div class="doctors-list">
      <div v-for="doctor in filteredDoctors" :key="doctor.doctorId" class="doctor-card">
        <div class="doctor-profile">
          <img :src="doctor.headshot" alt="Doctor's headshot" class="doctor-headshot" />
        
          <div class="doctor-info">
            <h2>{{ doctor.firstName }} {{ doctor.lastName }}</h2>
            <p>{{ doctor.specialty }}</p>
          </div>
        </div>

        <div class="doctor-actions">
          <button @click="navigateToBookAppointment(doctor.doctorId)">Create Appointment</button>
          <button v-on:click="$router.push({ name: 'leave-review', params: {doctorId: doctor.doctorId} })">Reviews</button>
        </div>
      </div>
    </div>
  </div>
</template>
  
<script>
import DoctorService from '../services/DoctorService';

export default {
  data() {
    return {
      searchQuery: '',
      doctors: [],
      loading: false
    };
  },
  computed: {
    filteredDoctors() {
      if (!this.searchQuery) return this.doctors;
      const searchLowerCase = this.searchQuery.toLowerCase();
      return this.doctors.filter(doctor => {
        return (
          doctor.firstName.toLowerCase().includes(searchLowerCase) ||
          doctor.lastName.toLowerCase().includes(searchLowerCase) ||
          doctor.specialty.toLowerCase().includes(searchLowerCase)
        );
      });
    }
  },
  methods: {
    async searchDoctors() {
      this.loading = true;
      try {
        const response = await DoctorService.getAllDoctors(this.searchQuery);
        this.doctors = response.data;
      } catch (error) {
        console.error('Error fetching doctors:', error);
      } finally {
        this.loading = false;
      }
    },
    navigateToBookAppointment(doctorId) {
      this.$router.push({ name: 'bookAppointment', params: { doctorId: doctorId } });
    },
    viewReviews(doctorId) {
      this.$router.push({ name: 'leave-review', params: { doctorId } });
    },
  },
  created() {
    this.searchDoctors();
  }
};
</script>

  
  <style scoped>
  .find-doctor-container {
    padding: 1rem;
  }
  
  .search-bar {
    margin-bottom: 2rem;
  }
  
  .search-bar input {
    width: 100%;
    padding: 0.5rem;
    margin-right: 0.5rem;
  }
  
  .search-bar button {
    padding: 0.5rem;
    cursor: pointer;
  }
  
  .doctors-list {
    display: flex;
    flex-direction: column;
    gap: 1rem;
  }
  
  .doctor-card {
    display: flex;
    align-items: center;
    justify-content: space-between;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
    text-align: left;
    padding: 1rem;
    border-radius: 8px;
    width: calc(100% - 2rem);
    margin: auto;
    border-right: 2px solid #ddd;
  }
  .doctor-profile {
  display: flex;
  align-items: center;
  gap: 1rem;
}

.doctor-headshot {
  width: 100px;
  height: 80px;
  border-radius: 30%;
  object-fit: cover;
}

  .doctor-info {
    display: flex;
    flex-direction: column;
    justify-content: center;
  }

  .doctor-actions {
    display: flex;
    margin-left: auto;
    gap: 10px;
  }
  
  .doctor-card button {
    background-color: #2720FF;
    color: white;
    padding: 0.5rem 1rem;
    border: none;
    border-radius: 4px;
    cursor: pointer;
  }
  
  .doctor-card button:hover {
    background-color: #B6E2EF;
  }
  </style>
