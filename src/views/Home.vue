<template>
  <div class="home">
    <div class="enterable1 dash-item-container metrics-overview-container">
      <div class="metrics-overview-item"><MetricOverview :value="36000000" :title="'Budget'" /></div>
      <div class="metrics-overview-item"><MetricOverview :value="450000" :title="'ROI'" /></div>
      <div class="metrics-overview-item"><MetricOverview :value="4350000" :title="'Annual Profit'" /></div>
      <div class="metrics-overview-item"><MetricOverview :value="45" :title="'Lead Conversion'" :percent="true" :fin="false" /></div>
      <div class="metrics-overview-item"><MetricOverview :value="1350000" :title="'Average Income'" :graph="true" /></div>
    </div>
    <div class="dash-item-container graphs-container">
      <div class=" enterable2 graph-item">
        <div class="chart bar-graph">
          
        </div>
      </div>
      <div class=" enterable2 graph-item graph-middle">
        <div class="chart line-graph">

        </div>
      </div>
      <div class=" enterable2 graph-item graph-right">
        <div class="chart pie-graph">

        </div>
      </div>
    </div>
    <div class="dash-item-container data-list-container">
      <div class="enterable3 data-list">
          <DataList 
          :title="'Employees'" 
          :test="true" 
          :expandable="false"
          :graphable="false"
          :data="testData" 
          :columnTitles="['NAME','EMAIL','COMPANY','SKILL','AGE']"
          :columnKeys="['name','email','company','skill', 'age']"
          @addToList="handleAdd()"
          @exportList="handleExport()"
           />
      </div>
    </div>
  </div>
</template>

<script>
import firebase from 'firebase';
import store from '../store';
import MetricOverview from '@/components/MetricOverview.vue';
import DataList from '@/components/DataList.vue';

export default {
  name: 'Home',
  components: {
    MetricOverview,
    DataList
  },
  data() {
    return {
      datetime: null,
      students: [],
      allGrades: [],
      totalAverage: 0,
      studentCount: 0,
      testData: [
        {
          name: 'Trent Brew',
          email: 'tbrew1023@gmail.com',
          company: 'LT Design Lab',
          skill: 'Web Development',
          age: 23,
          description: 'Web Developer living in Chicago'
        },
        {
          name: 'Lawan Alade-Fa',
          email: 'slafa@ltdesign.io',
          company: 'Studio LAFA',
          skill: 'Design',
          age: 23,
          description: 'Architect attending Northwestern'
        },
        {
          name: 'Eric Li',
          email: 'ezli@protonmail.com',
          company: 'Coinflip',
          skill: 'Graphic Design',
          age: 23,
          description: 'Graphic Designer living in Chicago'
        },
        {
          name: 'Tim Chatman',
          email: 'tchatman@ltdesign.io',
          company: 'LT Design Lab',
          skill: 'Data Science',
          age: 24,
          description: 'Data scientist attending DePaul'
        },
                {
          name: 'Lauren Brew',
          email: 'lbrew@gmail.com',
          company: 'froSkate',
          skill: 'Copyediting',
          age: 22,
          description: 'Skater, writer, activist'
        },
        {
          name: 'Candice Brew',
          email: 'candicebrew@gmail.com',
          company: 'FACTORY PR',
          skill: 'PR',
          age: 26,
          description: 'Branding genius living in New York'
        },
        {
          name: 'Virginia Brew',
          email: 'vbrew@gmail.com',
          company: 'LoanDepot',
          skill: 'Mortgage',
          age: 47,
          description: 'Me mama<3'
        },
        {
          name: 'Derrick Brew',
          email: 'dbrew@gmail.com',
          company: 'LoanDepot',
          skill: 'Mortgage',
          age: 47,
          description: 'Me papa<3'
        },
        {
          name: 'Manman',
          email: 'manman@gmail.com',
          company: 'dbjrFoundation',
          skill: 'People',
          age: 24,
          description: 'Love you manman'
        },
        {
          name: 'Jon Josko',
          email: 'jonj@gmail.com',
          company: 'Illinois Policy Institute',
          skill: 'Data Science',
          age: 24,
          description: 'Mario main'
        }
      ],
    }
  },
  mounted() {
    this.setDate();
    this.fetchStudents();
  },
  computed: { 
    dbOperations() {
      return store.state.dbOperations;
    },
    getCurrentStudent() {
      return store.state.currentStudent;
    }
  },
  watch: {
    dbOperations() {
      setTimeout(() => {
        this.students = [];
        this.fetchStudents();
        this.scrollToContext();
      }, 1000);
    }
  },
  methods: {
    setDate() {
      var today = new Date();
      var months = ['January','February','March','April','May','June','July','August','September','October','November','December'];
      var dd = String(today.getDate()).padStart(2, '0');
      var mm = String(today.getMonth() + 1).padStart(2, '0');
      var yyyy = today.getFullYear();

      var lastOfDay = dd.substring(dd.length-1,dd.length);
      var secondLastOfDay = dd.substring(dd.length-2,dd.length-1);
      today = months[parseInt(mm) - 1] + ' ' + dd + ( lastOfDay == 1 && secondLastOfDay != 1 ? 'st' : ( lastOfDay == 2 && secondLastOfDay != 1 ? 'nd' : ( lastOfDay == 3 && secondLastOfDay != 1 ? 'rd' : 'th' ) ) ) + ', ' + yyyy;

      this.datetime = today;
    },
    async fetchStudents() {
      await firebase.firestore().collection('students').get().then((docs) => {
        docs.forEach((doc) => {
          this.studentCount++;
          var preData = doc.data();
          var postData = { 
            ...preData, 
            name: preData.firstName + ' ' + preData.lastName,
            average: this.indiAverage(preData).toFixed(3),
          }
          if(!preData.tags) {
            postData = {
              ...postData,
              tags: []
            }
          }
          this.students.push(postData);
          for(let i = 0; i < doc.data().grades.length; i++) {
            this.allGrades.push(doc.data().grades[i]);
          }
        });
        store.commit('studentCount', this.studentCount);
        this.getAverage();
      });
    },
    getAverage() {
      var sum = 0;
      for(let i = 0; i < this.allGrades.length; i++) {
        sum += parseInt(this.allGrades[i]);
      }
      this.totalAverage = parseInt((sum / this.allGrades.length).toFixed(0));
    },
    indiAverage(student) {
      var sum = 0;
      for(let i = 0; i < student.grades.length; i++) {
        sum += parseInt(student.grades[i]);
      }
      return sum / student.grades.length;
    },
    scrollToContext() {
      var context = this.getCurrentStudent;
      setTimeout(() => {
        console.clear();
        var contextElement = document.getElementById(context);
        var topPos = contextElement.offsetTop;
        document.getElementById('scrollable-inner').scrollTop = topPos - 526;
      }, 2300).catch((err) => {
        console.log('no scroll context: ', err);
      });
    }
  }
}
</script>

<style lang="scss" scoped>
@import '../assets/styles/global';

$headHeight: 300px;

.home {
  height: calc(100vh - 80px);
  width: 100%;
  background: #eee;
  margin-top: 80px;
  padding: 24px;
  box-sizing: border-box;
}

.data-container {
  height: 100%;
  width: 100%;
  border-radius: $rad;
}

.banner-container {
  background-image: url('https://github.com/tbrew1023/icommon/blob/master/images/banner-small.png?raw=true');
  background-size: cover;
  background-repeat: no-repeat;
  background-position: center;
  height: 100%;
  width: 100%;
  margin-right: $gap;
  border-radius: $rad;

}

.progress-circle-container {
  background: $colorMainLight;
  height: 100%;
  width: 100%;
  border-radius: $rad;
  display: flex;
  justify-content: center;
  align-items: center;
}

.circle-progress {
  transform: scale(0.6);
}

.div1 { grid-area: 1 / 10 / 5 / 13; }
.div2 { grid-area: 1 / 1 / 5 / 10; }
.div3 { grid-area: 5 / 1 / 13 / 13; }

.metrics-overview-container {
  //background: black;
  width: 100%;
  height: 16%;
  display: flex;

  .metrics-overview-item {
    width: 100%;
    //border: red solid 1px;
    display: flex;
    align-items: center;
    justify-content: center;
  }
}

.graphs-container {
  //background:#212121;
  height: 42%;
  width: 100%;
  display: flex;
}

.data-list-container {
  //background: gray;
  height: 42%;
  width: 100%;
}

.dash-item-container {
  padding: 24px;
  box-sizing: border-box;
}

.graph-item {
  //border: solid red 1px;
  width: 100%;
  height: 100%;
  border-radius: 24px !important;
}

.graph-middle {
  margin-left: 24px;
  margin-right: 24px;
}

.data-list {
  //border: solid 1px red;
  width: 100%;
  height: 100%;
}

.enterable1 {
  opacity: 0;
  transform: scale(0.9);
  animation: flyin 600ms ease forwards 1300ms;
}

.enterable2 {
  opacity: 0;
  transform: scale(0.9);
  animation: flyin 600ms ease forwards 1400ms;
}

.enterable3 {
  opacity: 0;
  transform: scale(0.9);
  animation: flyin 600ms ease forwards 1500ms;
}

.bar-graph {
  background-position: center;
  background-repeat: no-repeat;
  background-size: contain;
  background-image: url('../assets/bar-graph-example.png');
  height: inherit;
  width: 100%;
}

.line-graph {
  background-position: center;
  background-repeat: no-repeat;
  background-size: contain;
  background-image: url('../assets/line-graph-example.png');
  height: inherit;
  width: 100%;
}

.pie-graph {
  background-position: center;
  background-repeat: no-repeat;
  background-size: contain;
  background-image: url('../assets/pie-graph-example.png');
  height: inherit;
  width: 100%;
}

.chart {
  border-radius: 12px;
}

@keyframes flyin {
  to {
    opacity: 1;
    transform: scale(1);
  }
}

</style>
