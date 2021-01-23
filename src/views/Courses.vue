<template>
  <div>
    <h1>Kursy - Wszystkie</h1>
    <div>
      <div class="course" v-for="course in courses">
        <div class="title">{{ course.title }}</div>
        <div class="description">{{ course.description }}</div>
        <div class="options">
          <button color="primary" plain @click="OpenDialog(course)">Edytuj</button>
          <button color="warning" plain
                 @click="$router.push({name: 'Exams', query: {courseId: course.id, title: course.title}})">Przejdź
          </button>
          <button color="error" plain @click="RemoveCourse(course)">Usuń</button>
        </div>
      </div>
      <div class="course">
        <div class="title">
          <input type="text" v-model="newCourse.title" placeholder="Nazwa kursu" class="form" color="white"></input>
        </div>
        <div class="description">
          <input type="text" v-model="newCourse.description" placeholder="Opis kursu" class="form"></input>
        </div>
        <div class="options">
          <button @click="AddNewCourse">Dodaj</button>
        </div>
      </div>
    </div>
   
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: "Course",
  data: () => {
    return {
      courses: [],
      newCourse: {
        title: null,
        description: null
      },
      editCourseDialog: false,
      editedCourse: {
        id: null,
        title: null,
        description: null,
        oldName: null
      },
      snackbar: false
    }
  },
  methods: {
    AddNewCourse() {
      const formData = new FormData()
      formData.append('title', this.newCourse.title)
      formData.append('description', this.newCourse.description)

      axios.post(
          'https://zaliczenie.btry.eu/api/Course',
          formData,
          {
            headers: {
              'Authorization': `Bearer ${this.$store.state.token}`,
              'Content-Type': 'multipart/form-data'
            }
          })
          .then(response => {
            this.courses.push(response.data.record)
            this.newCourse.description = null
            this.newCourse.title = null
          })
      this.snackbar=true
    },
    RemoveCourse(course) {
      axios.delete(
          `https://zaliczenie.btry.eu/api/Course/${course.id}`,
          {
            headers: {
              'Authorization': `Bearer ${this.$store.state.token}`
            }
          }
      ).then(() => {
        this.courses = this.courses.filter((value, index, arr) => {
          return value !== course
        })
        this.snackbar=true
      })
    },
    EditCourse() {
      const formData = new FormData()
      formData.append('id', this.editedCourse.id)
      formData.append('title', this.editedCourse.title)
      formData.append('description', this.editedCourse.description)
      axios.put(`https://zaliczenie.btry.eu/api/Course`,
          formData,
          {
            headers: {
              'Authorization': `Bearer ${this.$store.state.token}`,
              'Content-Type': 'multipart/form-data'
            }
          }
      )
          .then(() => {
            this.editCourseDialog = false
            this.courses.map(i => {
              if (i.id === this.editedCourse.id) {
                i.title = this.editedCourse.title
                i.description = this.editedCourse.description
              }
              return i
            })
            this.editedCourse.id = null
            this.editedCourse.title = null
            this.editedCourse.oldName = null
            this.editedCourse.description = null
          })
      this.snackbar=true

    },
    OpenDialog(course) {
      this.editCourseDialog = true
      this.editedCourse.id = course.id
      this.editedCourse.title = course.title
      this.editedCourse.oldTitle = course.title
      this.editedCourse.description = course.description
    }
  },
  mounted() {
    axios.get(
        'https://zaliczenie.btry.eu/api/Course',
        {headers: {'Authorization': `Bearer ${this.$store.state.token}`}}
    )
        .then(response => {
          this.courses = response.data.records
        })
  }
}
</script>

<style scoped lang="scss">
	input{
		background-color:#a5d5ee;
	}

  .form {
    margin: 20;
  }
  button{
    padding:20px;
    background-color: #a5d5ee;
    color: #14658f;
    border: 1px solid #2f7ea7;
  }

</style>