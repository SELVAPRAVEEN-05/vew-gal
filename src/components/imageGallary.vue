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
        <!-- Favorite toggle button top-left. Uses .stop so it doesn't open the lightbox -->
        <button
          class="fav-btn"
          :class="{ active: image.fav }"
          @click.stop="toggleFav(indexMap[index])"
          :aria-pressed="image.fav"
          :title="image.fav ? 'Unfavorite' : 'Add to favorites'"
        >
          <span v-if="image.fav">❤</span>
          <span v-else>♡</span>
        </button>

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
          src: "https://img.freepik.com/premium-photo/anime-style-illustration-full-moon-clouds_1096472-11180.jpg",
          alt: "moon dark",
          title: "moon dark",
          description: "the moon in the dark sky",
          fav: false,
        },
        {
          src: "https://i.ytimg.com/vi/TFvASHo9jr8/hq720.jpg?sqp=-oaymwEhCK4FEIIDSFryq4qpAxMIARUAAAAAGAElAADIQj0AgKJD&rs=AOn4CLBzYqNKk_iN50K0bbDFT7LDTGOSYg",
          alt: "Anime",
          title: "anime boy",
          description: "this is an anime",
          fav: false,
        },
        {
          src: "https://images.stockcake.com/public/8/2/d/82d90271-085d-4357-bcef-e098a9dab547_large/majestic-black-panther-stockcake.jpg",
          alt: "Dark Wallpaper",
          title: "Black Panther",
          description: "A majestic black panther in the wild",
          fav: false,
        },
        {
          src: "https://wallpapers.com/images/hd/dark-butterfly-1280-x-1024-wallpaper-kbt9qd2pwga32znx.jpg",
          alt: "Blue Butterfly",
          title: "Blue Butterfly",
          description: "A beautiful blue butterfly in the sky",
          fav: false,
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
      toggleFav(index) {
        // toggle favorite for image at real index
        if (typeof index === "number" && this.images[index]) {
          this.images[index].fav = !this.images[index].fav;
        }
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

/* Favorite button shown on top-left of each image */
.fav-btn {
  position: absolute;
  top: 10px;
  left: 10px;
  width: 36px;
  height: 36px;
  border-radius: 50%;
  border: none;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  background: rgba(255, 255, 255, 0.85);
  color: #ff3b3b;
  font-size: 1.05rem;
  cursor: pointer;
  box-shadow: 0 2px 6px rgba(0,0,0,0.2);
  transition: transform 0.15s, background 0.15s;
  z-index: 5; /* above image */
}

.fav-btn:hover { transform: scale(1.05); }

.fav-btn.active {
  background: #fff0f0;
  color: #d40000;
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

