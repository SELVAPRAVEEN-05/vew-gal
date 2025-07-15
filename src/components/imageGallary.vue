<template>
  <div class="gallery-container">
    <h2>🌿 Nature Gallery</h2>

    <!-- Filter and Sort Controls -->
    <div class="controls">
      <input v-model="filterText" placeholder="🔍 Filter by title..." />
      <select v-model="sortOrder">
        <option disabled value="">Sort by</option>
        <option value="asc">Title A → Z</option>
        <option value="desc">Title Z → A</option>
      </select>
    </div>

    <!-- Gallery Display -->
    <div class="gallery">
      <div
        v-for="(image, index) in sortedFilteredImages"
        :key="index"
        class="gallery-item"
      >
        <img :src="image.src" :alt="image.alt" @click="selectImage(indexMap[index])" />
        <button class="delete-btn" @click="deleteImage(indexMap[index])">Delete</button>
      </div>
    </div>

    <!-- Lightbox -->
    <div v-if="isLightboxActive" class="lightbox">
      <button @click="prevImage" class="nav-btn">⟨</button>
      <img
        :src="selectedImage.src"
        :alt="selectedImage.alt"
        @wheel="zoomImage"
        :style="{ transform: 'scale(' + zoom + ')' }"
      />
      <button @click="nextImage" class="nav-btn">⟩</button>
      <button @click="closeLightbox" class="close-btn">X</button>

      <div class="meta">
        <label>
          Title:
          <input v-model="selectedImage.title" />
        </label>
        <label>
          Description:
          <textarea v-model="selectedImage.description" />
        </label>
      </div>
    </div>

    <!-- Restore Deleted -->
    <button
      v-if="deletedImages.length"
      class="restore-btn"
      @click="restoreImage"
    >
      Restore Last Deleted
    </button>
  </div>
</template>

<script>
export default {
  name: "ImageGallery",
  data() {
    return {
      images: [
        {
          src: "https://i.pinimg.com/736x/20/21/54/20215479b52f4a638d9868da9e7965e4.jpg",
          alt: "moon dark",
          title: "moon dark",
          description: "the moon in the dark sky",
        },
        {
          src: "https://avatarfiles.alphacoders.com/339/thumb-1920-339627.jpg",
          alt: "Anime",
          title: "anime boy",
          description: "this is an anime",
        },
        {
          src: "https://cdn.cssauthor.com/wp-content/uploads/2020/11/Collection-of-Excellent-Dark-Wallpapers.jpg?strip=all&lossy=1&ssl=1",
          alt: "Dark Wallpaper",
          title: "Black Panther",
          description: "A majestic black panther in the wild",
        },
        {
          src: "https://thumbs.dreamstime.com/b/butterfly-blue-sky-sun-24906248.jpg",
          alt: "Blue Butterfly",
          title: "Blue Butterfly",
          description: "A beautiful blue butterfly in the sky",
        },
      ],
      deletedImages: [],
      selectedImageIndex: null,
      zoom: 1,
      filterText: "",
      sortOrder: "",
    };
  },

  computed: {
    isLightboxActive() {
      return this.selectedImageIndex !== null;
    },
    selectedImage() {
      return this.images[this.selectedImageIndex] || {};
    },
    sortedFilteredImages() {
      let filtered = this.images.filter((img) =>
        img.title.toLowerCase().includes(this.filterText.toLowerCase())
      );

      if (this.sortOrder === "asc") {
        filtered.sort((a, b) => a.title.localeCompare(b.title));
      } else if (this.sortOrder === "desc") {
        filtered.sort((a, b) => b.title.localeCompare(a.title));
      }

      return filtered;
    },
    indexMap() {
      return this.sortedFilteredImages.map((img) =>
        this.images.indexOf(img)
      );
    },
  },

  methods: {
    selectImage(index) {
      this.selectedImageIndex = index;
      this.zoom = 1;
    },
    closeLightbox() {
      this.selectedImageIndex = null;
    },
    deleteImage(index) {
      const removed = this.images.splice(index, 1)[0];
      this.deletedImages.push(removed);
      if (index === this.selectedImageIndex) this.closeLightbox();
    },
    restoreImage() {
      if (this.deletedImages.length) {
        this.images.push(this.deletedImages.pop());
      }
    },
    zoomImage(event) {
      this.zoom += event.deltaY > 0 ? -0.1 : 0.1;
      this.zoom = Math.max(0.5, Math.min(this.zoom, 2));
    },
    prevImage() {
      if (this.selectedImageIndex > 0) {
        this.selectedImageIndex--;
        this.zoom = 1;
      }
    },
    nextImage() {
      if (this.selectedImageIndex < this.images.length - 1) {
        this.selectedImageIndex++;
        this.zoom = 1;
      }
    },
  },
};
</script>

<style scoped>
.gallery-container {
  min-height: 100vh;
  background-color: #00eaff;
  text-align: center;
  padding: 40px 20px;
  font-family: "Segoe UI", sans-serif;
}

h2 {
  font-size: 2.5rem;
  margin-bottom: 20px;
  color: #064469;
}

.controls {
  margin-bottom: 30px;
}

.controls input,
.controls select {
  padding: 10px 15px;
  font-size: 1rem;
  margin: 0 8px;
  border-radius: 8px;
  border: 1px solid #bbb;
  outline: none;
  transition: all 0.3s;
}

.controls input:focus,
.controls select:focus {
  border-color: #0077ff;
  box-shadow: 0 0 5px rgba(0, 123, 255, 0.4);
}

.gallery {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 25px;
}

.gallery-item {
  position: relative;
  transition: transform 0.3s;
  border-radius: 15px;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.15);
}

.gallery-item:hover {
  transform: scale(1.05);
}

.gallery-item img {
  width: 400px;
  height: 500px;
  object-fit: cover;
  cursor: pointer;
  border-radius: 15px;
}

.delete-btn {
  position: absolute;
  top: 10px;
  right: 10px;
  background: crimson;
  color: white;
  border: none;
  padding: 6px 10px;
  cursor: pointer;
  border-radius: 6px;
  font-weight: bold;
  font-size: 0.9rem;
}

.restore-btn {
  margin-top: 30px;
  background: #2980b9;
  color: white;
  padding: 12px 25px;
  border: none;
  cursor: pointer;
  border-radius: 8px;
  font-size: 1rem;
  transition: background 0.3s;
}

.restore-btn:hover {
  background: #21618c;
}

.lightbox {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.85);
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  z-index: 9999;
  padding: 20px;
  overflow: auto; /* scrolls if content is larger */
}

.lightbox img {
  max-width: 90vw;
  max-height: 80vh;
  transition: transform 0.3s;
  border-radius: 10px;
  margin: 20px 0;
  object-fit: contain;
  cursor: grab;
}

.nav-btn {
  background: transparent;
  color: white;
  font-size: 3rem;
  border: none;
  cursor: pointer;
  user-select: none;
  margin: 0 20px;
}

.close-btn {
  position: absolute;
  top: 25px;
  right: 30px;
  background: red;
  color: white;
  padding: 12px 16px;
  cursor: pointer;
  border-radius: 6px;
  font-size: 1rem;
}

.meta {
  background: white;
  padding: 20px;
  margin-top: 20px;
  width: 350px;
  border-radius: 10px;
  text-align: left;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.2);
}

.meta input,
.meta textarea {
  width: 100%;
  margin-top: 8px;
  margin-bottom: 15px;
  padding: 10px;
  font-size: 1rem;
  border-radius: 6px;
  border: 1px solid #ccc;
  resize: vertical;
}
</style>

