<template>
  <div class="feed-container">
    <filter-sidebar @filter-changed="handleFilterChange" />
    <div class="main-content">
      <post-creation @create-post="addPost" />
      <div class="posts-list">
        <div v-for="post in filteredPosts" :key="post.id" class="post-card">
          <router-link :to="`/profile/${post.created_by}`" class="post-header">
            <img :src="userAvatar" alt="User Avatar" class="avatar" />
            <div class="post-info">
              <h3>{{ post.created_by }}</h3>
              <p class="date">{{ formatDate(post.createdAt) }}</p>
            </div>
          </router-link>
          <div class="post-content">
            <h2>{{ post.title }}</h2>
            <p>{{ post.description }}</p>
            <p><strong>Purpose:</strong> {{ post.purpose }}</p>
            <p><strong>Skills:</strong> {{ post.requiredSkills ? post.requiredSkills.join(', ') : 'None' }}</p>
            <p><strong>Location:</strong> {{ post.location }}</p>
            <p><strong>Tags:</strong> {{ post.tags ? post.tags.join(', ') : 'None' }}</p>
            <p><strong>Deadline:</strong> {{ formatDate(post.deadline) }}</p>
            <p><strong>Team Size:</strong> {{ post.teamSize }}</p>
          </div>
          <div class="post-actions">
            <button class="apply-btn" @click="openApplyModal(post)">Apply</button>
          </div>
        </div>
      </div>
      <div v-if="filteredPosts.length === 0" class="no-posts">
        <p>No posts available. Create one to get started!</p>
      </div>
    </div>
    <div class="news-section">
      <h3>Tech News Updates</h3>
      <div class="news-list">
        <div v-for="news in newsItems" :key="news.id" class="news-card">
          <h4>{{ news.title }}</h4>
          <p>{{ news.summary }}</p>
          <a :href="news.link" target="_blank" class="news-link">Read More</a>
          <p class="news-date">{{ formatDate(news.publishedAt) }}</p>
        </div>
      </div>
      <div v-if="newsItems.length === 0" class="no-news">
        <p>No news available at the moment.</p>
      </div>
    </div>
    <apply-modal v-if="selectedPostId" :post-id="selectedPostId" @close="selectedPostId = null" @submit="handleApply" />
  </div>
</template>

<script>
import avatarImage from '@/assets/images/avatar.jpg';
import ApplyModal from '@/components/ApplyModal.vue';
import FilterSidebar from '@/components/FilterSidebar.vue';
import PostCreation from '@/components/PostCreation.vue';

export default {
  name: 'FeedView',
  components: { PostCreation, ApplyModal, FilterSidebar },
  data() {
    return {
      userAvatar: avatarImage,
      posts: [
        {
          id: '1',
          created_by: 'user123',
          title: 'Web Development Project',
          description: 'Build a responsive e-commerce website.',
          purpose: 'Create a platform for small businesses.',
          requiredSkills: ['JavaScript', 'Vue', 'CSS'],
          location: 'Remote',
          tags: ['web', 'frontend', 'backend'],
          deadline: '2025-06-01',
          teamSize: 4,
          createdAt: '2025-05-02T10:00:00Z',
          updatedAt: '2025-05-02T10:00:00Z',
        },
        {
          id: '2',
          created_by: 'user456',
          title: 'Mobile App Development',
          description: 'Develop a fitness tracking app.',
          purpose: 'Promote healthy lifestyles.',
          requiredSkills: ['React Native', 'TypeScript'],
          location: 'New York',
          tags: ['mobile', 'health'],
          deadline: '2025-07-15',
          teamSize: 3,
          createdAt: '2025-05-01T12:00:00Z',
          updatedAt: '2025-05-01T12:00:00Z',
        },
        {
          id: '3',
          created_by: 'user789',
          title: 'AI Research Collaboration',
          description: 'Join a team to develop an AI-powered chatbot.',
          purpose: 'Advance conversational AI technology.',
          requiredSkills: ['Python', 'TensorFlow', 'NLP'],
          location: 'San Francisco',
          tags: ['AI', 'machine learning', 'research'],
          deadline: '2025-08-01',
          teamSize: 5,
          createdAt: '2025-05-03T09:00:00Z',
          updatedAt: '2025-05-03T09:00:00Z',
        },
      ],
      filteredPosts: [],
      currentFilters: {},
      newsItems: [
        {
          id: 1,
          title: 'Breakthrough in Quantum Computing',
          summary: 'Researchers achieve new milestone in quantum processor speed.',
          link: 'https://example.com/news/quantum-computing',
          publishedAt: '2025-05-01T08:00:00Z',
        },
        {
          id: 2,
          title: 'AI Startups to Watch in 2025',
          summary: 'A look at emerging AI companies disrupting the tech industry.',
          link: 'https://example.com/news/ai-startups',
          publishedAt: '2025-05-02T10:30:00Z',
        },
        {
          id: 3,
          title: 'New Web Development Frameworks',
          summary: 'Explore the latest tools for building modern web applications.',
          link: 'https://example.com/news/web-frameworks',
          publishedAt: '2025-05-03T12:00:00Z',
        },
      ],
      selectedPostId: null,
    };
  },
  mounted() {
    // Initialize filteredPosts with all posts
    this.filteredPosts = [...this.posts];
  },
  methods: {
    addPost(post) {
      const newPost = {
        ...post,
        id: `${this.posts.length + 1}`,
        createdAt: new Date().toISOString(),
        updatedAt: new Date().toISOString(),
      };
      this.posts.unshift(newPost);
      this.applyFilters(this.currentFilters); // Reapply filters to include new post
      console.log('New post added:', newPost);
    },
    formatDate(dateStr) {
      if (!dateStr) return 'N/A';
      const date = new Date(dateStr);
      return isNaN(date.getTime()) ? 'Invalid Date' : date.toLocaleDateString('en-GB');
    },
    openApplyModal(post) {
      this.selectedPostId = post.id;
      console.log('Opened apply modal for post:', post.id);
    },
    handleApply(application) {
      console.log('Application submitted for post:', this.selectedPostId, application);
      this.selectedPostId = null;
    },
    handleFilterChange(filters) {
      this.currentFilters = filters;
      this.applyFilters(filters);
    },
    applyFilters(filters) {
      if (Object.keys(filters).length === 0) {
        this.filteredPosts = [...this.posts];
        return;
      }

      this.filteredPosts = this.posts.filter(post => {
        let match = true;

        if (filters.skills && filters.skills.length > 0) {
          const postSkills = post.requiredSkills?.map(s => s.toLowerCase()) || [];
          const filterSkills = filters.skills.map(s => s.toLowerCase());
          match = match && filterSkills.some(skill => postSkills.includes(skill));
        }

        if (filters.purpose) {
          match = match && post.purpose.toLowerCase().includes(filters.purpose.toLowerCase());
        }

        if (filters.tags && filters.tags.length > 0) {
          const postTags = post.tags?.map(t => t.toLowerCase()) || [];
          const filterTags = filters.tags.map(t => t.toLowerCase());
          match = match && filterTags.some(tag => postTags.includes(tag));
        }

        if (filters.location) {
          match = match && post.location.toLowerCase().includes(filters.location.toLowerCase());
        }

        if (filters.teamSize) {
          match = match && post.teamSize === filters.teamSize;
        }

        if (filters.title) {
          match = match && post.title.toLowerCase().includes(filters.title.toLowerCase());
        }

        if (filters.deadline) {
          const postDeadline = new Date(post.deadline);
          const filterDeadline = new Date(filters.deadline);
          match = match && postDeadline <= filterDeadline;
        }

        if (filters.uploadDate) {
          const postUploadDate = new Date(post.createdAt);
          const filterUploadDate = new Date(filters.uploadDate);
          match = match && postUploadDate >= filterUploadDate;
        }

        return match;
      });
    },
  },
};
</script>

<style scoped>
body {
  overflow: hidden;
}

.feed-container {
  display: flex;
  width: 90vw;
  height: 95vh;
  margin-left: 8rem;
  margin-bottom: 1rem;
    margin-top: 1rem;
    padding: 2rem;
    background: linear-gradient(135deg, #e6f4ea 0%, #c3e8d1 100%);
    border-radius: 16px;
    box-shadow: 0 8px 20px rgba(0, 0, 0, 0.1);
    transition: box-shadow 0.3s ease;
    overflow: hidden;
  }
  
  .feed-container:hover {
    box-shadow: 0 12px 28px rgba(0, 0, 0, 0.15);
  }
  
  .main-content {
    flex: 2;
    margin-right: 2rem;
    height: calc(100vh - 9rem);
    overflow-y: auto;
  }
  
  .news-section {
    flex: 1;
    padding: 1.5rem;
    background: #ffffff;
    border-radius: 12px;
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.05);
    height: calc(100vh - 9rem);
    overflow-y: auto;
  }
  
  .news-section h3 {
    font-size: 1.2rem;
    color: #2d3748;
    margin-bottom: 1rem;
    font-weight: 600;
  }
  
  .news-list {
    display: flex;
    flex-direction: column;
    gap: 1rem;
  }
  
  .news-card {
    padding: 1rem;
    background: #f7fafc;
    border-radius: 8px;
    transition: transform 0.2s ease, box-shadow 0.2s ease;
  }
  
  .news-card:hover {
    transform: translateY(-3px);
    box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
  }
  
  .news-card h4 {
    font-size: 1.1rem;
    color: #1a202c;
    margin: 0 0 0.5rem;
  }
  
  .news-card p {
    font-size: 0.95rem;
    color: #4a5568;
    margin: 0 0 0.5rem;
    line-height: 1.5;
  }
  
  .news-link {
    color: #41b883;
    text-decoration: none;
    font-weight: 500;
    transition: color 0.2s ease;
  }
  
  .news-link:hover {
    color: #2ecc71;
  }
  
  .news-date {
    font-size: 0.85rem;
    color: #718096;
    margin-top: 0.5rem;
  }
  
  .no-news {
    text-align: center;
    padding: 1rem;
    color: #4a5568;
    font-size: 1rem;
  }
  
  .main-content::-webkit-scrollbar,
  .news-section::-webkit-scrollbar {
    width: 8px;
  }
  
  .main-content::-webkit-scrollbar-track,
  .news-section::-webkit-scrollbar-track {
    background: #e6f4ea;
    border-radius: 10px;
  }
  
  .main-content::-webkit-scrollbar-thumb,
  .news-section::-webkit-scrollbar-thumb {
    background-color: #41b883;
    border-radius: 10px;
  }
  
  .main-content,
  .news-section {
    scrollbar-width: thin;
    scrollbar-color: #41b883 #e6f4ea;
  }
  
  .posts-list {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
    gap: 1.5rem;
    margin-top: 2rem;
  }
  
  .post-card {
    background: #ffffff;
    border-radius: 12px;
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.05);
    padding: 1.5rem;
    transition: transform 0.2s ease, box-shadow 0.2s ease;
  }
  
  .post-card:hover {
    transform: translateY(-5px);
    box-shadow: 0 6px 18px rgba(0, 0, 0, 0.1);
  }
  
  .post-header {
    display: flex;
    align-items: center;
    gap: 1.25rem;
    margin-bottom: 1rem;
    text-decoration: none;
  }
  
  .avatar {
    width: 50px;
    height: 50px;
    border-radius: 50%;
    object-fit: cover;
    border: 3px solid #41b883;
    transition: border-color 0.3s ease;
  }
  
  .avatar:hover {
    border-color: #2ecc71;
  }
  
  .post-info h3 {
    font-size: 1.2rem;
    font-weight: 600;
    color: #2d3748;
    margin: 0;
    transition: color 0.2s ease;
  }
  
  .post-info h3:hover {
    color: #41b883;
  }
  
  .post-info .date {
    font-size: 0.9rem;
    color: #718096;
    margin: 0.25rem 0 0;
  }
  
  .post-content {
    margin-bottom: 1.5rem;
  }
  
  .post-content h2 {
    font-size: 1.5rem;
    font-weight: 700;
    color: #1a202c;
    margin: 0.75rem 0;
    line-height: 1.3;
  }
  
  .post-content p {
    margin: 0.5rem 0;
    color: #4a5568;
    line-height: 1.6;
    font-size: 1rem;
  }
  
  .post-content strong {
    color: #2d3748;
    font-weight: 500;
  }
  
  .post-actions {
    text-align: right;
  }
  
  .apply-btn {
    background-color: #41b883;
    color: #ffffff;
    padding: 0.75rem 1.75rem;
    border: none;
    border-radius: 8px;
    cursor: pointer;
    font-size: 1rem;
    font-weight: 500;
    transition: background-color 0.3s ease, transform 0.2s ease;
    box-shadow: 0 2px 6px rgba(65, 184, 131, 0.3);
  }
  
  .apply-btn:hover {
    background-color: #2ecc71;
    transform: translateY(-2px);
    box-shadow: 0 4px 10px rgba(46, 204, 113, 0.4);
  }
  
  .apply-btn:active {
    transform: translateY(0);
    box-shadow: 0 1px 3px rgba(65, 184, 131, 0.3);
  }
  
  .no-posts {
    text-align: center;
    padding: 2rem;
    color: #4a5568;
    font-size: 1.1rem;
    background: #ffffff;
    border-radius: 12px;
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.05);
  }
  
  @media (max-width: 1200px) {
    .feed-container {
      max-width: 95vw;
      flex-wrap: wrap;
      height: auto;
      overflow: hidden;
    }
  
    .main-content {
      flex: 1 1 100%;
      margin-right: 0;
      margin-bottom: 2rem;
      height: 80vh;
    }
  
    .news-section {
      flex: 1 1 100%;
      height: 80vh;
    }
  }
  
  @media (max-width: 1024px) {
    .feed-container {
      flex-direction: column;
      height: auto;
    }
  }
  
  @media (max-width: 768px) {
    .feed-container {
      max-width: 90vw;
      padding: 1rem;
      border-radius: 8px;
    }
  
    .posts-list {
      grid-template-columns: 1fr;
      gap: 1rem;
    }
  
    .post-card {
      padding: 1rem;
    }
  
    .post-header {
      gap: 1rem;
    }
  
    .avatar {
      width: 40px;
      height: 40px;
    }
  
    .post-content h2 {
      font-size: 1.25rem;
    }
  
    .post-content p {
      font-size: 0.95rem;
    }
  
    .post-actions {
      text-align: center;
    }
  
    .apply-btn {
      width: 100%;
      padding: 0.5rem 1rem;
    }
  
    .news-card h4 {
      font-size: 1rem;
    }
  
    .news-card p {
      font-size: 0.9rem;
    }
  }
  
  @media (max-width: 480px) {
    .feed-container {
      max-width: 100vw;
      padding: 0.5rem;
    }
  
    .post-info h3 {
      font-size: 1.1rem;
    }
  
    .post-info .date {
      font-size: 0.85rem;
    }
  
    .news-card h4 {
      font-size: 0.95rem;
    }
  
    .news-card p {
      font-size: 0.85rem;
    }
}
</style>
