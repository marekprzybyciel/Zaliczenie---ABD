<template>
  <div class="exam-header">
    <h1>Egzaminy {{ $route.query.title }}</h1>
    <div>
      <div class="course" v-for="exam in exams">
        <div class="header">
          <div class="title">{{ exam.description }}</div>

        </div>
        <div class="options">
          <button plain @click="OpenEditDialog(exam)">Edytuj</button>
          <button plain @click="SetPass(exam)" v-show="!exam.isPassed">Oznacz jako zdany</button>
          <button plain @click="RemoveExam(exam)">Usuń</button>
        </div>
      </div>
      <div class="course">
        <div class="header">
          <input type="Text" v-model="newExam.description" placeholder="Opis egzaminu" class="form" color="white"
                        ></input>
        </div>
        <div class="description">
        </div>
        <div class="options">
          <button @click="AddNewExam">Dodaj</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Exams",
  data: () => {
    return {
      exams: [],
      newExam: {
        description: null
      },
      snackbar: false,
      editedExam: {
        id: null,
        description: null,
        oldDescription: null,
        courseId: null
      },
      editDialog: false
    }
  },
  mounted() {
    axios.get(`https://zaliczenie.btry.eu/api/Course/${this.$route.query.courseId}`,
        {
          headers: {Authorization: `Bearer ${this.$store.state.token}`}
        })
        .then(response => {
          this.exams = response.data.record.exams
        })
  },
  methods: {
    AddNewExam() {
      axios.post(`https://zaliczenie.btry.eu/api/Exams`,
          {
            description: this.newExam.description,
            courseId: this.$route.query.courseId
          },
          {
            headers: {Authorization: `Bearer ${this.$store.state.token}`}
          })
          .then(response => {
            this.exams.push(response.data.record)
            this.newExam.description = null
            this.snackbar = true
          })
    },
    SetPass(exam) {
      axios.put(`https://zaliczenie.btry.eu/api/Exams/${exam.id}`,
          {},
          {
            headers: {Authorization: `Bearer ${this.$store.state.token}`}
          })
          .then(() => {
            this.exams = this.exams.map(i => {
              if (i.id === exam.id) i.isPassed = true
              return i
            })
            this.snackbar = true
          })
    },
    RemoveExam(exam) {
      axios.delete(`https://zaliczenie.btry.eu/api/Exams/${exam.id}`,
          {
            headers: {Authorization: `Bearer ${this.$store.state.token}`}
          })
          .then(() => {
            this.exams = this.exams.filter(i => {
              if (i.id !== exam.id) return i
            })
            this.snackbar = true
          })
    },
    OpenEditDialog(exam) {
      this.editedExam.description = exam.description
      this.editedExam.id = exam.id
      this.editedExam.oldDescription = exam.description
      this.editedExam.courseId = exam.courseId
      this.editDialog = true
    },
    EditExam() {
      console.log(this.editedExam)
      axios.put(`https://zaliczenie.btry.eu/api/Exams`,
          this.editedExam,
          {
            headers: {Authorization: `Bearer ${this.$store.state.token}`}
          })
          .then(() => {
                this.exams = this.exams.map(i => {
                  if (i.id === this.editedExam.id) i.description = this.editedExam.description
                  return i
                })
                this.snackbar = true
              }
          )
    }
  }

}
</script>

<style scoped >
input{
		background-color:#a5d5ee;
	}
  
  button{

    padding:20px;
    background-color: #a5d5ee;
    color: #14658f;
    border: 1px solid #2f7ea7;
  }

</style>