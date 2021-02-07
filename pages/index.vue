<template>
  <div class="container">
    <div class="login" v-if="!loginVisible">
      <div class="headText">Not Hesaplama Sistemi</div>
      <form @submit.prevent="login()">
        <vs-input v-model="username" placeholder="Kullanıcı adı">
          <template #icon>
            <font-awesome-icon icon="user" />
          </template>
        </vs-input>

        <vs-input v-model="password" type="password" placeholder="Password">
          <template #icon>
            <font-awesome-icon icon="lock" />
          </template>
        </vs-input>

        <vs-button class="signIn">
          <font-awesome-icon icon="sign-in-alt" /> Giriş
        </vs-button>

        <vs-alert gradient v-if="loginError">
          <template #title>
            Giriş hatalı
          </template>
        </vs-alert>
      </form>
    </div>
    <div class="layout" v-if="loginVisible">
      <div class="header">
        <div class="headText">Öğrenci Not Hesaplama Sistemi</div>
        <div class="addNewLineBtn">
          <vs-button @click="addStudent()">Öğrenci Ekle</vs-button>
          
          <vs-button warn >
            <download-excel :data="students" worksheet="Öğenci Not Sistemi" name="Öğrenci-not-sistemi.xls"
              :export-fields="{
              'No': 'no',
              'İsim Soyisim': 'nameSurname',
              'Vize Notu': 'midTerm',
              'Vize Yüzde': 'midTermPercent',
              'Final Notu': 'finalExam',
              'Final Yüzde': 'finalExamPercent',
              'Öğrenci Notu': 'finalScore',
              'Harf Notu': 'letterScore',
              }"
            >Excel indir</download-excel>
          </vs-button>

          <vs-button @click="logOut()">Çıkış</vs-button>
        </div>  
      </div>
      <div class="studentTable">
        <vs-table>
          <template #thead>
            <vs-tr>
              <vs-th>Öğrenci No</vs-th>
              <vs-th>İsim Soyisim</vs-th>
              <vs-th>Vize Notu</vs-th>
              <vs-th>Vize <vs-input class="midTermPercentDefault" v-model="midTermPercentDefault" placeholder="Yüzde" /></vs-th>
              <vs-th>Final Notu</vs-th>
              <vs-th>Final <vs-input class="finalTermPercentDefault" v-model="finalTermPercentDefault" placeholder="Yüzde" /></vs-th>
              <vs-th>Öğrenci Notu</vs-th>
              <vs-th>Harf Notu</vs-th>
            </vs-tr>
          </template>
          <template #tbody>
            <vs-tr :key="index" v-for="(student, index) in students" :data="index">
              <vs-td><vs-input v-model="student.no" placeholder="Öğrenci No" /></vs-td>
              <vs-td><vs-input v-model="student.nameSurname" placeholder="İsim Soyisim" /></vs-td>
              <vs-td><vs-input v-model="student.midTerm" placeholder="Vize Notu" /></vs-td>
              <vs-td class="colCenter"> {{ student.midTermPercent = (student.midTerm / 100) * midTermPercentDefault }}</vs-td>
              <vs-td><vs-input v-model="student.finalExam" placeholder="Final Notu" /></vs-td>
              <vs-td class="colCenter"> {{ student.finalExamPercent = (student.finalExam / 100) * finalTermPercentDefault }}</vs-td>
              <vs-td class="colCenter">
                {{ student.finalScore = ((student.midTerm / 100) * midTermPercentDefault) + (student.finalExam / 100) * finalTermPercentDefault }}
              </vs-td>
              <vs-td class="colCenter">
                <span v-if="
                ((student.midTerm / 100) * midTermPercentDefault) + (student.finalExam / 100) * finalTermPercentDefault >= 95 && 
                ((student.midTerm / 100) * midTermPercentDefault) + (student.finalExam / 100) * finalTermPercentDefault <= 100">
                  {{ student.letterScore = 'A' }}
                </span>

                <span :value="student.letterScore" v-else-if="
                ((student.midTerm / 100) * midTermPercentDefault) + (student.finalExam / 100) * finalTermPercentDefault >= 90 && 
                ((student.midTerm / 100) * midTermPercentDefault) + (student.finalExam / 100) * finalTermPercentDefault <= 94">
                  {{ student.letterScore = 'A-' }}
                </span>

                <span v-else-if="
                ((student.midTerm / 100) * midTermPercentDefault) + (student.finalExam / 100) * finalTermPercentDefault >= 85 && 
                ((student.midTerm / 100) * midTermPercentDefault) + (student.finalExam / 100) * finalTermPercentDefault <= 89">
                  {{ student.letterScore = 'B+' }}
                </span>

                <span v-else-if="
                ((student.midTerm / 100) * midTermPercentDefault) + (student.finalExam / 100) * finalTermPercentDefault >= 80 && 
                ((student.midTerm / 100) * midTermPercentDefault) + (student.finalExam / 100) * finalTermPercentDefault <= 84">
                  {{ student.letterScore = 'B' }}
                </span>

                <span v-else-if="
                ((student.midTerm / 100) * midTermPercentDefault) + (student.finalExam / 100) * finalTermPercentDefault >= 75 && 
                ((student.midTerm / 100) * midTermPercentDefault) + (student.finalExam / 100) * finalTermPercentDefault <= 79">
                  {{ student.letterScore = 'B-' }}
                </span>

                <span v-else-if="
                ((student.midTerm / 100) * midTermPercentDefault) + (student.finalExam / 100) * finalTermPercentDefault >= 70 && 
                ((student.midTerm / 100) * midTermPercentDefault) + (student.finalExam / 100) * finalTermPercentDefault <= 74">
                  {{ student.letterScore = 'C+' }}
                </span>

                <span v-else-if="
                ((student.midTerm / 100) * midTermPercentDefault) + (student.finalExam / 100) * finalTermPercentDefault >= 65 && 
                ((student.midTerm / 100) * midTermPercentDefault) + (student.finalExam / 100) * finalTermPercentDefault <= 69">
                  {{ student.letterScore = 'C' }}
                </span>

                <span v-else-if="
                ((student.midTerm / 100) * midTermPercentDefault) + (student.finalExam / 100) * finalTermPercentDefault >= 60 && 
                ((student.midTerm / 100) * midTermPercentDefault) + (student.finalExam / 100) * finalTermPercentDefault <= 64">
                  {{ student.letterScore = 'C-' }}
                </span>

                <span v-else-if="
                ((student.midTerm / 100) * midTermPercentDefault) + (student.finalExam / 100) * finalTermPercentDefault >= 55 && 
                ((student.midTerm / 100) * midTermPercentDefault) + (student.finalExam / 100) * finalTermPercentDefault <= 59">
                  {{ student.letterScore = 'D+' }}
                </span>

                <span v-else-if="
                ((student.midTerm / 100) * midTermPercentDefault) + (student.finalExam / 100) * finalTermPercentDefault >= 50 && 
                ((student.midTerm / 100) * midTermPercentDefault) + (student.finalExam / 100) * finalTermPercentDefault <= 54">
                  {{ student.letterScore = 'D' }}
                </span>

                <span v-else-if="
                ((student.midTerm / 100) * midTermPercentDefault) + (student.finalExam / 100) * finalTermPercentDefault > 0 && 
                ((student.midTerm / 100) * midTermPercentDefault) + (student.finalExam / 100) * finalTermPercentDefault <= 49">
                  {{ student.letterScore = 'F1' }}
                </span>
              </vs-td>
            </vs-tr>
          </template>
        </vs-table>
      </div>
    </div>
  </div>
</template>

<script>

export default {

  data() {
    return {
      username: '',
      password: '',
      loginVisible: false,
      loginError: false,

      midTermPercentDefault: '',
      finalTermPercentDefault: '',
      students: [
        { 
          no : null,
          nameSurname : '',
          midTerm : null,
          midTermPercent : null,
          finalExam : null,
          finalExamPercent : null,
          finalScore : null,
          letterScore : '',
        },
      ]
    }
  },
  methods : {
    logOut() {
      localStorage.clear();
      this.loginVisible = false;
    },
    login() {
      if(this.username == 'cky' && this.password == '8784') {
        localStorage.setItem('loginVisible', true);
        this.loginVisible = true;
      } else {
        this.loginError = true;
      }
    },
    addStudent() {
      this.students.push(
        { 
          no : null,
          nameSurname : '',
          midTerm : null,
          midTermPercent : null,
          finalExam : null,
          finalExamPercent : null,
          finalScore : null,
          letterScore : '',
        },
      )
    }
  },
  mounted() {
    if(localStorage.getItem('loginVisible') == 'true') {
      this.loginVisible = true;
    }
  }
}
</script>