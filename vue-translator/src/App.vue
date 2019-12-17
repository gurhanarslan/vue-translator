<template>
  <div class="container-fluid">
    <div class="text-center text-muted">
      <small>@gurhanarslan</small>
      <br />
      <small>2019</small>
    </div>
    <div class="row">
      <div class="col-12 text-center bg-darkblue text-white">
        <h2>Vue-Translator</h2>
      </div>
      <div class="col-12 col-md-5 text-center">
        <div class="form-group">
          <div>
            <strong>Çevireceğiniz Metin</strong>
          </div>
          <hr />
          <textarea
            style="border:2px solid rgb(47, 47, 83)!important"
            class="form-control shadow"
            cols="30"
            rows="10"
            placeholder="Çevirmek istediğiniz metni buraya giriniz."
            v-model="translate"
          ></textarea>
        </div>
      </div>
      <div class="col-md-2 mt-10">
        <select v-model="selected" class="form-control" @change="choosing">
          <option disabled selected value>Dili Seçiniz</option>
          <option v-for="lang in language" :value="lang.langCode">{{lang.lang}}</option>
        </select>
        <div class="text-center text-danger small">{{err}}</div>
        <button class="mt-3 btn form-control bg-light" @click="translateTo">Çevir</button>
      </div>
      <div class="col-12 col-md-5 text-center">
        <div class="form-group">
          <div>
            <strong>Çevrilen Metin</strong>
          </div>
          <hr />

          <textarea class="form-control" disabled name id cols="30" rows="10"> {{translateText[0]}} </textarea>
        </div>
      </div>
    </div>
    <div  v-if="this.history.length>0">
      <div class="text-center bg-darkblue text-white">
        <h3>Arama Geçmişi</h3>
      </div>
      <hr />

      <table class="table table-striped w-100">
        <thead>
          <tr>
            <th>Metin</th>
            <th>Dil</th>
            <th>Çevrilen Metin</th>
          </tr>
        </thead>
        <tbody>
          <tr class="hover" @click="againSearch(hist.from,hist.lang)" v-for="hist in history">
            <td>{{hist.from}}</td>
            <td>{{hist.lang}}</td>
            <td>{{hist.to[0]}}</td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
export default {
  name: "app",
  data() {
    return {
      translate: "",
      translateText: "",
      language: [],
      selected: "",
      selectedLang: "",
      err: "",
      history: []
    };
  },
  methods: {
    choosing() {
      console.log(this.selected);
    },
    againSearch(value, lang) {
      this.translate = value;
      this.selected = lang;
      this.translateTo();
    },
    translateTo() {
      if (this.selected == "") {
        this.err = "* Lütfen Bir dil seçiniz!";
      } else {
        this.err = "";

        this.$http
          .get(
            "https://translate.yandex.net/api/v1.5/tr.json/translate?key=trnsl.1.1.20191217T094730Z.0f486fb41ca7e50a.9c023a39118b7d3683d2bb65d5eaf01e2e42c107&ui=tr&text=" +
              this.translate +
              "&lang=" +
              this.selected
          )
          .then(response => {
            let data = response.data.text;
            this.translateText = data;

            this.history.push({
              from: this.translate,
              to: this.translateText,
              lang: this.selected
            });
          });
     
        this.history.reverse();

    
      }
    }
  },
  created() {
    this.$http
      .get(
        "https://translate.yandex.net/api/v1.5/tr.json/getLangs?key=trnsl.1.1.20191217T094730Z.0f486fb41ca7e50a.9c023a39118b7d3683d2bb65d5eaf01e2e42c107&ui=tr"
      )
      .then(response => {
        let lang = response.data.langs;
        for (let key in lang) {
          this.language.push({ langCode: key, lang: lang[key] });
        }

        console.log(this.language);
      });
  }
};
</script>

<style>
.mt-10 {
  margin-top: 10rem;
}
.bg-darkblue {
  background-color: rgb(47, 47, 83);
  padding: 10px;
  border-bottom: 2px solid rgb(103, 103, 172);
}
.hover:hover {
  background-color: rgb(245, 191, 191) !important;
  transition: all 0.5s linear;
  cursor: pointer;
}
</style>
