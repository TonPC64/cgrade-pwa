<template>
  <div class="app">
    <header>
      Grade Calculator
    </header>

    <div class="columns is-marginless is-mobile is-centered">
      <b-field 
      label="เกรดเฉลี่ยยรวม" 
      class="column is-half grade">
        {{total | twopoint}}
      </b-field>
      <b-field
      v-if="term.grade > 0 && term.grade !== Infinity" 
      label="เกรดเทอมนี้" 
      class="column is-half grade">
        {{term.grade | twopoint}}
      </b-field>
    </div>
    
    <div class="columns is-marginless is-mobile">
        <b-field label="แต้มระดับคะแนน" class="column is-half">
          <b-input min="0" type="number" v-model="point"></b-input>
        </b-field>
        <b-field label="หน่วยกิตที่ลง" class="column is-half">
          <b-input min="0" type="number" v-model="unit"></b-input>
        </b-field>
    </div>
    <div class="columns is-marginless is-mobile" v-if="grades.length !== 0">
      <b-field 
        label="เกรด" 
        class="column is-full" >
        <div 
        class="centered"
        :key="index"
        v-for="(grade, index) in grades">
          <b-input class="input-gap" v-model="grade.name" placeholder="วิชา"></b-input>
          <b-select class="input-gap" v-model="grade.unit">
            <option
              v-for="unit in unitOption"
              :value="unit"
              :key="'unit-' + unit">
              {{ unit }}
            </option>
          </b-select>
          <b-select class="input-gap" v-model="grade.grade">
            <option
              v-for="grade in gradeOption"
              :value="grade"
              :key="'grade-' + grade">
              {{ grade }}
            </option>
          </b-select>
          <div class="input-gap middle remove" @click="remove(index)">
            <b-icon  pack="fa" icon="trash" type="is-danger"></b-icon>
          </div>
        </div>
      </b-field>
    </div>
    
    <div class="centered">
      <div class="button add is-primary" @click="addSubject()">
        <b-icon  pack="fa" icon="plus-circle"></b-icon>
        <span>เพิ่มวิชา</span>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  filters: {
    twopoint (val) {
      if (val >= 0 && val !== Infinity) {
        return val.toFixed(2)
      }
      return (0).toFixed(2)
    }
  },
  data () {
    return {
      point: 0,
      unit: 0,
      unitOption: [3, 2, 1],
      gradeOption: ['A', 'B+', 'B', 'C+', 'C', 'D+', 'D', 'F'],
      grades: []
    }
  },
  computed: {
    total () {
      return ((+this.point + +this.term.point) / (+this.unit + +this.term.unit))
    },
    term () {
      const unit = this.grades.reduce((prev, curr) => prev + curr.unit, 0)
      const point = this.grades.map(({grade, unit}) => {
        let gradepoint = 0
        if (grade === 'A') {
          gradepoint = 4
        } else if (grade === 'B+') {
          gradepoint = 3.5
        } else if (grade === 'B') {
          gradepoint = 3
        } else if (grade === 'C+') {
          gradepoint = 2.5
        } else if (grade === 'C') {
          gradepoint = 2
        } else if (grade === 'D+') {
          gradepoint = 1.5
        } else if (grade === 'D') {
          gradepoint = 1
        }
        return gradepoint * unit
      }, 0).reduce((prev, curr) => prev + curr, 0)
      return {unit, point, grade: (point / unit)}
    }
  },
  watch: {
    point () {
      this.save()
    },
    unit () {
      this.save()
    },
    grades: {
      handler () {
        this.save()
      },
      deep: true
    }
  },
  methods: {
    addSubject () {
      this.grades.push({
        name: '',
        unit: 3,
        grade: 'A'
      })
    },
    remove (index) {
      this.grades.splice(index, 1)
    },
    save () {
      window.localStorage.setItem('total', JSON.stringify({point: this.point, unit: this.unit}))
      window.localStorage.setItem('term', JSON.stringify(this.grades))
    },
    load () {
      const total = window.localStorage.getItem('total')
      const term = window.localStorage.getItem('term')
      let jsonTotal = {}
      let jsonTerm = {}
      if (total) {
        jsonTotal = JSON.parse(total)
        this.point = +jsonTotal.point
        this.unit = +jsonTotal.unit
      }
      if (term) {
        jsonTerm = JSON.parse(term)
        this.grades = jsonTerm
      }
    }
  },
  created () {
    this.load()
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
  .main {
    box-sizing: border-box;
    padding: 15px;
  }
  header {
    font-size: 1.7rem;
    font-weight: bolder;
    padding: 15px;
    width: 100%;
    text-align: center;
  }
  .app {
    width: 100%;
  }
  .grade {
    font-size: 3.5rem;
    font-weight: bolder;
    text-align: center;
  }
  .add {
    cursor: pointer;
    width: fit-content;
  }
  .add:hover {
    color: cadetblue;
  }
  .input-gap {
    margin: 5px;
  }
  .centered {
    display: flex;
    justify-content: center;
  }
  .middle {
    height: 36px;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  .remove {
    cursor: pointer;
  }
</style>
