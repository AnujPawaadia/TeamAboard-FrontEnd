<template>
  <div class="business-deal-container">
    <router-link to="/visitor" class="user-menu">
      <img :src="user.avatar" alt="User Avatar" class="user-avatar" />
    </router-link>
    <div class="background-blur"></div>
    <div class="content-wrapper" v-scroll-reveal="{ origin: 'bottom', distance: '40px', duration: 1000, delay: 100 }">
      <div class="text-section">
        <h1 class="title">Your Collaborative HUB</h1>
        <p class="description">
          Ignite your learning with our innovative educational social media platform. We are more than just a network;
          we are your committed partner for growth. We provide you with the essential resources to thrive: connect with
          inspiring mentors, discover transformative internships, participate in motivating competitions, and access
          informative blogs. Our platform fosters a collaborative environment where you can collaborate on projects,
          learn from a supportive community, and grow towards your educational aspirations. Together, we are building
          the future of learning.
        </p>
        <div class="cta-buttons">
          <router-link to="/feed" class="cta-button">
            <i class="fas fa-user-tie"></i> Mentors <span class="arrow">→</span>
          </router-link>
          <router-link to="/feed" class="cta-button">
            <i class="fas fa-briefcase"></i> Internships <span class="arrow">→</span>
          </router-link>
          <router-link to="/feed" class="cta-button">
            <i class="fas fa-trophy"></i> Competitions <span class="arrow">→</span>
          </router-link>
          <router-link to="/feed" class="cta-button">
            <i class="fas fa-blog"></i> Blogs <span class="arrow">→</span>
          </router-link>
          <router-link to="/feed" class="cta-button">
            <i class="fas fa-blog"></i> More <span class="arrow">→</span>
          </router-link>
        </div>
      </div>
      <div class="image-section">
        <img :src="businessPeopleImage" alt="Business deal" class="business-img" />
      </div>
    </div>
  </div>
</template>

<script>
import avatarImage from '@/assets/images/avatar.jpg';
import businessPeopleImage from '@/assets/images/home.png';
import ScrollReveal from 'scrollreveal';

export default {
  name: 'HomePage',
  data() {
    return {
      user: {
        avatar: avatarImage,
        id: 'current-user-id' // Add user ID or username
      },
      businessPeopleImage
    };
  },
  mounted() {
    ScrollReveal().reveal('.content-wrapper', {
      origin: 'bottom',
      distance: '50px',
      duration: 1000,
      delay: 200,
      easing: 'ease-in-out'
    });
  }
};
</script>

<style scoped>
@font-face {
  font-family: 'HappyDaySchool';
  src: url('@/assets/font/HappyDaySchoolOpposed-Black.ttf') format('truetype');
  font-weight: normal;
  font-style: normal;
}

.logo {
  position: absolute;
  top: 0px;
  left: 100px;
  font-size: 150px;
  font-family: 'HappyDaySchool', sans-serif;
  color: #0c502c;
  text-transform: capitalize;
  letter-spacing: 1px;
  z-index: 1000;
}

.business-deal-container {
  position: relative;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  overflow: hidden;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

.user-menu {
  position: absolute;
  top: 20px;
  right: 20px;
  z-index: 100;
  cursor: pointer;
}

.user-avatar {
  width: 100px;
  height: 100px;
  border-radius: 50%;
  object-fit: cover;
  border: 2px solid #24AD6A;
  transition: transform 0.3s ease, border-color 0.3s ease;
}

.user-avatar:hover {
  transform: scale(1.1);
  border-color: #1C8C55;
}

.background-blur {
  position: absolute;
  top: 20%;
  left: 60%;
  width: 400px;
  height: 400px;
  background: radial-gradient(circle, rgba(36, 173, 106, 0.3), transparent 70%);
  filter: blur(100px);
  z-index: 0;
  /* Removed pointer-events: none */
}

.content-wrapper {
  position: relative;
  z-index: 2;
  display: flex;
  flex-wrap: wrap;
  max-width: 1200px;
  width: 100%;
  align-items: center;
  justify-content: space-between;
  gap: 40px;
}

.text-section {
  flex: 1 1 500px;
}

.title {
  font-size: 52px;
  font-weight: 800;
  color: #0C502C;
  margin-bottom: 20px;
  line-height: 1.2;
}

:deep(a) {
  text-decoration: none;
}

:deep(a:hover) {
  text-decoration: none;
}

.user-name {
  font-size: 1.5rem;
  font-weight: 500;
  color: #2c3e50;
  margin-right: 10px;
  text-decoration: none;
  transition: color 0.3s ease;
  cursor: pointer;
}

.description {
  font-size: 18px;
  line-height: 1.7;
  color: #333;
  margin-bottom: 30px;
}

.cta-buttons {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
}

.cta-button {
  display: inline-flex;
  align-items: center;
  gap: 10px;
  background: linear-gradient(135deg, #24AD6A, #1C8C55);
  color: white;
  font-size: 16px;
  padding: 12px 28px;
  border-radius: 40px;
  font-weight: 600;
  text-decoration: none;
  transition: all 0.3s ease;
  box-shadow: 0 6px 12px rgba(0, 0, 0, 0.1);
  /* Added explicit pointer-events */
  pointer-events: auto;
}

.cta-button i {
  font-size: 16px;
}

.cta-button:hover {
  transform: translateY(-2px);
  background: linear-gradient(135deg, #1C8C55, #157245);
  box-shadow: 0 12px 20px rgba(0, 0, 0, 0.15);
}

.arrow {
  font-size: 18px;
  transition: transform 0.2s ease;
}

.cta-button:hover .arrow {
  transform: translateX(4px);
}

.image-section {
  flex: 1 1 500px;
  display: flex;
  justify-content: center;
  align-items: center;
}

.business-img {
  max-width: 130%;
  height: auto;
}

@media (max-width: 768px) {
  .content-wrapper {
    flex-direction: column;
    text-align: center;
  }

  .title {
    font-size: 36px;
  }

  .description {
    font-size: 16px;
  }

  .cta-buttons {
    flex-direction: column;
    align-items: center;
  }

  .cta-button {
    width: 100%;
    justify-content: center;
  }
}
</style>

