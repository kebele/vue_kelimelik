<template>
  <div id="app" class="container">
    
    <div class="card mt-3">
      <div class="card-body" v-if="tamamlandi">
        tebrikler yarışmayı {{ this.puan }} puan ile tamamladınız
      </div>
    </div>

    <div class="card mt-4" v-if="!mevcutSoru">
      <div class="card-header">
      <h4 class="mb-0">
        kelime oyununa hoş geldin
      </h4>
    </div>
      <div class="card-body">
        yarışmaya başlamak için başla butonuna basın
      </div>
      <div class="card-footer">
        <button class="btn btn-primary" @click="basla">yarışmaya başla</button>
      </div>
    </div>
    <div class="card mt-4" v-else>
      <div class="card-header">
        <h3 class="mb-0">{{ mevcutSoru.soru }}</h3>
      </div>
      <!-- harfler bölümü -->
      <!-- burayı komponent yaptığımız için aşağıdaki şekilde düzenleyeceğiz -->
      <div class="card-body">
        <div class="d-flex">
          <Harf :deger="harf.harf" :acik="harf.acildi" v-for="(harf, index) in harfler"
            :key="'harf-' + index" />
          <!-- <div
            class="harf shadow mr-3"
            v-for="(harf, index) in harfler"
            :key="'harf-' + index"
          >
            <span v-if="harf.acildi">{{ harf.harf }}</span>
            <span v-else></span>
          </div> -->
        </div>
      </div>
      <div class="card-footer">
        <div class="d-flex">
          <div class="mr-4">toplam puan : {{ this.puan }}</div>
          <div class="mr-4">
            kalan sure : <kbd>{{ kalanSure }}</kbd> saniye
          </div>
          <div>harf puanı : {{ this.harfPuan }}</div>
        </div>
      </div>
<div class="card-footer" :class="mesajClass" v-if="mesaj">{{ mesaj }}</div>
      <div class="card-footer">
        <div class="input-group">
          <input
            type="text"
            class="form-control"
            placeholder="cevabı giriniz"
            v-model="yarismaciCevap"
            @keyup="yarismaciCevap = yarismaciCevap.toLocaleUpperCase('tr')"
          />
          <div class="input-group-append">
            <!-- <p>cevabınız : {{ yarismaciCevap }}</p> -->
            <button @click="cevapVer" class="btn btn-success">cevap ver</button>
          </div>
        </div>
      </div>
      <div class="card-footer">
        <button class="btn btn-secondary" @click="harfVer">harf ver</button>
      </div>
    </div>
  </div>
</template>

<script>
// import Harf from '@/components/Harf.vue'
//main.js de tanımladığımız için burada gerek kalmadı
export default {
  name: "App",
  components: {
    // Harf,
    //main.js de tanımladık
  },
  data() {
    return {
      sorular: [
        {
          soru: "siyah ile aynı anlama gelen renk?",
          cevap: "KARA",
          soruldu: false,
        },
        {
          soru: "sık kullanlan bir isim",
          cevap: "AHMET",
          soruldu: false,
        },
        {
          soru: "Türkiyenin başkenti",
          cevap: "ANKARA",
          soruldu: false,
        },
        {
          soru: "Karadenizde bir ilimiz",
          cevap: "TRABZON",
          soruldu: false,
        },
      ],
      mesaj : null,
      mesajClass : "",
      mesajSure : null,
      mevcutSoru: null,
      cevap: "",
      harfler: [],
      puan: 0,
      harfPuan: 0,
      yarismaciCevap: "",
      tamamlandi: false,
      //yarışmanın bitmesi
      sure: null,
      kalanSure: 0,
    };
  },
  methods: {
    mesajGoster(mesaj, tur){
      this.mesaj = mesaj
      if( tur === "hata"){
        this.mesajClass = "bg-danger text-white"
      } else if ( tur === "basari"){
       this.mesajClass = "bg-success text-white" 
      } else {
        this.mesajClass = "bg-dark text-white"
      }
      if(this.mesajSure){
        clearTimeout(this.mesajSure)
        this.mesajSure = null
      }
      this.mesajSure = setTimeout(() => {
        this.mesaj = null
      }, 3000);
    },
    basla() {
      this.tamamlandi = false;
      this.mevcutSoru = null;
      this.puan = 0;
      this.sorular.map((x) => {
        x.soruldu = false;
      });
      this.kalanSure = 240; //4dk
      //yarışmada 4dk veriyoruz yarışma başladığında süre bundan azalmaya başlayacak,
      this.sure = setInterval(() => {
        this.kalanSure--;
        if (this.kalanSure === 0) {
          this.bitir();
        }
      }, 1000);
      this.soruVer();
      this.mesajGoster("iyi yarışmalar")
    },
    bitir() {
      this.tamamlandi = true;
      this.mevcutSoru = null;
      clearInterval(this.sure);
    },
    soruVer() {
      this.yarismaciCevap = "";
      this.mevcutSoru = this.sorular.find((x) => !x.soruldu);
      //soruldu : false olan ik soruyu bul

      if (!this.mevcutSoru) {
        //soru yoksa/kalmadıysa
        // this.tamamlandi = true
        this.bitir();
        return;
      }
      this.harfler = [];
      this.mevcutSoru.cevap.split("").map((x) => {
        this.harfler.push({
          harf: x,
          acildi: false,
        });
      });
      this.harfPuan = this.harfler.length * 100;
      this.mevcutSoru.soruldu = true;
    },
    harfVer() {
      //açılmamış harfi bulalım
      let rastgeleHarfIndex = Math.floor(Math.random() * this.harfler.length);

      //son harf kaldıysa harf vermesin
      if (this.harfPuan <= 100) {
        return;
      }

      let harf = this.harfler[rastgeleHarfIndex];

      while (harf.acildi) {
        rastgeleHarfIndex = Math.floor(Math.random() * this.harfler.length);
        harf = this.harfler[rastgeleHarfIndex];
      }
      //harf açık old. sürece döngüyü çalıştır

      console.log(rastgeleHarfIndex);
      harf.acildi = true;
      this.harfPuan -= 100;
    },

    cevapVer() {

      // if(!this.yarismaciCevap.length){
      //   return;
      // }
      if(this.yarismaciCevap.length !== this.harfler.length){
        this.mesajGoster("harf sayıları aynı değil")
        return;
      }

      let cevap = this.yarismaciCevap.toLocaleUpperCase("tr");
      this.yarismaciCevap = cevap;

      if (
        this.yarismaciCevap === this.mevcutSoru.cevap.toLocaleUpperCase("tr")
      ) {
        // alert("tebrikler")
        this.puan += this.harfPuan;
        this.mesajGoster("tebrikler", "basari")
      } else {
         this.mesajGoster(`yanlış cevap doğrusu ${this.mevcutSoru.cevap} olmalıydı`, "hata")
        this.puan -= this.harfPuan;
      }
      this.soruVer();
      //
    },
  },
};
</script>

<style>

</style>
