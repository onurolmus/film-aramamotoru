<!DOCTYPE html>
<html lang="tr">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Film/Dizi Arama Motoru</title>
  <style>:root {
      --bg-color: #111;
      --text-color: white;
      --card-bg: #222;
      --btn-bg: #f39c12;
      --btn-color: black;
    }
    .light {
      --bg-color: #f0f0f0;
      --text-color: #111;
      --card-bg: #fff;
      --btn-bg: #2980b9;
      --btn-color: white;
    }
    body {
      font-family: Arial, sans-serif;
      background-color: var(--bg-color);
      color: var(--text-color);
      padding: 20px;
      transition: all 0.3s ease;
      position: relative;
      min-height: 100vh;
      display: flex;
      flex-direction: column;
      align-items: center;
      max-width: 1200px;
      margin: 0 auto;
    }
    input, button {
      padding: 10px;
      font-size: 16px;
    }
    #sonuclar, #favoriler {
      max-width: 700px;
      background-color: var(--card-bg);
      padding: 20px;
      border-radius: 10px;
      box-shadow: 0 0 10px rgba(0,0,0,0.5);
      display: flex;
      flex-wrap: wrap;
      gap: 15px;
      justify-content: center;
      margin: 20px auto 0 auto;
    }
    h1, h2 {
      text-align: center;
    }
    .film {
      background-color: var(--card-bg);
      padding: 10px;
      border-radius: 10px;
      width: 200px;
      cursor: pointer;
      transition: transform 0.2s ease;
      display: flex;
      flex-direction: column;
      align-items: center;
    }
    .film:hover {
      transform: scale(1.05);
    }
    .film img {
      width: 100%;
      border-radius: 5px;
    }
    .film h3 {
      font-size: 18px;
      margin: 10px 0 5px;
      text-align: center;
    }
    .favori-btn {
      margin-top: 5px;
      padding: 5px 10px;
      background-color: var(--btn-bg);
      border: none;
      color: var(--btn-color);
      cursor: pointer;
      border-radius: 5px;
    }
    .tema-btn {
      position: absolute;
      top: 20px;
      right: 20px;
      font-size: 20px;
      padding: 8px 12px;
      border: none;
      border-radius: 6px;
      cursor: pointer;
      background-color: var(--card-bg);
      color: var(--text-color);
    }
    #filmDetay {
      margin-top: 30px;
      max-width: 700px;
      background-color: var(--card-bg);
      padding: 20px;
      border-radius: 10px;
      box-shadow: 0 0 10px rgba(0,0,0,0.5);
    }
    #filmDetay img {
      width: 200px;
      float: left;
      margin-right: 20px;
      border-radius: 5px;
    }
    #filmDetay h2 {
      margin-top: 0;
    }
    #filmDetay p {
      margin: 5px 0;
    }
    #filmDetay::after {
      content: "";
      display: block;
      clear: both;
    }
    .arama-alani {
      display: flex;
      gap: 10px;
      margin-top: 10px;
      justify-content: center;
      flex-wrap: wrap;
    }
    .arama-alani input {
      flex: 1;
      max-width: 300px;
    }
    /* Puanlama için yıldızlar */
    .puanlama span {
      font-size: 22px;
      cursor: pointer;
      color: gray;
      user-select: none;
      margin: 0 2px;
    }
    .puanlama span.dolu {
      color: gold;
    }</style>
</head>
<body>
  <button class="tema-btn" onclick="temayiDegistir()" id="temaButonu">🌙</button>
  <h1>🎥 Film/Dizi Arama Motoru</h1>
  <div class="arama-alani">
    <input type="text" id="aramaKutusu" placeholder="Film adı girin...">
    <button onclick="filmAra()">Ara</button>
  </div>

  <h2>📽️ Arama Sonuçları</h2>
  <div id="sonuclar"></div>

    <h2>🎬 Film/Dizi Detayları</h2>
  <div id="filmDetay"></div>

  <h2>⭐ Favoriler</h2>
  <div id="favoriler"></div>


  <div id="yorumKutusu" style="display:none; position: fixed; top: 20px; left: 50%; transform: translateX(-50%);
background: var(--card-bg); color: var(--text-color); padding: 15px; border-radius: 10px; box-shadow: 0 0 15px rgba(0,0,0,0.7);
max-width: 400px; z-index: 1000;">
  <h3 id="yorumFilmBaslik"></h3>
  <textarea id="yorumInput" rows="4" style="width:100%; border-radius:5px; padding:8px;" placeholder="Yorumunuzu yazın..."></textarea>
  <div style="margin-top: 10px; text-align: right;">
    <button onclick="yorumKutusuKapat()" style="margin-right: 10px; padding:6px 12px; cursor:pointer;">İptal</button>
    <button onclick="yorumKaydet()" style="padding:6px 12px; cursor:pointer; background-color: var(--btn-bg); color: var(--btn-color); border:none; border-radius:5px;">Gönder</button>
  </div>
</div>

<script>const API_KEY = 'c37b74b4'; // OMDb API anahtarı

   function filmAra() {
  const arama = document.getElementById('aramaKutusu').value.trim();
  if (!arama) return;

  fetch(`https://www.omdbapi.com/?apikey=${API_KEY}&s=${arama}`)
    .then(res => res.json())
    .then(data => {
      const container = document.getElementById('sonuclar');
      container.innerHTML = '';
      if (data.Search) {
        data.Search.forEach(film => {
          const div = document.createElement('div');
          div.className = 'film';
          div.onclick = () => filmDetayGoster(film.imdbID);

          div.innerHTML = `
            <img src="${film.Poster !== "N/A" ? film.Poster : ''}" alt="Poster">
            <h3>${film.Title}</h3>
            <p>${film.Year}</p>
            <button class="favori-btn" onclick='event.stopPropagation(); favoriyeEkle(${JSON.stringify(film).replace(/'/g, "&apos;")})'>Favoriye Ekle</button>
            <div class="puanlama-kart" style="margin-top: 8px;">Puan ver : </div>
          `;

          // Yıldızları ekle
          const puanlamaDiv = div.querySelector('.puanlama-kart');
          for (let i = 1; i <= 5; i++) {
            const star = document.createElement('span');
            star.textContent = '☆';
            star.style.fontSize = '20px';
            star.style.cursor = 'pointer';
            star.style.color = '#ccc';
            star.dataset.deger = i;
            puanlamaDiv.appendChild(star);
          }

          const stars = puanlamaDiv.querySelectorAll('span');

          stars.forEach((star, index) => {
            star.addEventListener('mouseover', () => {
              stars.forEach((s, idx) => {
                s.style.color = idx <= index ? 'gold' : '#ccc';
              });
            });

            star.addEventListener('mouseout', () => {
              let puanlar = JSON.parse(localStorage.getItem('puanlar')) || {};
              let mevcutPuan = puanlar[film.imdbID] || 0;
              stars.forEach((s, idx) => {
                s.style.color = idx < mevcutPuan ? 'gold' : '#ccc';
              });
            });

            star.addEventListener('click', (e) => {
              e.stopPropagation();
              puanKaydet(film.imdbID, index + 1);
              yorumKutusuAc(film);
            });
          });

          // Mevcut puanı göster
          let puanlar = JSON.parse(localStorage.getItem('puanlar')) || {};
          let mevcutPuan = puanlar[film.imdbID] || 0;
          stars.forEach((s, idx) => {
            s.style.color = idx < mevcutPuan ? 'gold' : '#ccc';
          });

          container.appendChild(div);
        });
      } else {
        container.innerHTML = '<p>Film bulunamadı.</p>';
      }
    });
}



    function favoriyeEkle(film) {
      let favoriler = JSON.parse(localStorage.getItem('favoriler')) || [];
      if (!favoriler.find(f => f.imdbID === film.imdbID)) {
        favoriler.push(film);
        localStorage.setItem('favoriler', JSON.stringify(favoriler));
        favorileriGoster();
      }
    }

    function favoridenCikar(imdbID) {
      let favoriler = JSON.parse(localStorage.getItem('favoriler')) || [];
      favoriler = favoriler.filter(f => f.imdbID !== imdbID);
      localStorage.setItem('favoriler', JSON.stringify(favoriler));
      favorileriGoster();
    }

function favorileriGoster() {
  let favoriler = JSON.parse(localStorage.getItem('favoriler')) || [];
  // Bozuk kayıtları temizle
  favoriler = favoriler.filter(film => film && film.imdbID);
  // Temiz listeyi kaydet
  localStorage.setItem('favoriler', JSON.stringify(favoriler));

  const favoriAlan = document.getElementById('favoriler');
  favoriAlan.innerHTML = '';
  favoriler.forEach(film => {
    const div = document.createElement('div');
    div.className = 'film';
    div.onclick = () => filmDetayGoster(film.imdbID);
    div.innerHTML = `
      <img src="${film.Poster !== "N/A" ? film.Poster : ''}" alt="Poster">
      <h3>${film.Title}</h3>
      <p>${film.Year}</p>
      <button class="favori-btn" onclick='event.stopPropagation(); favoridenCikar("${film.imdbID}")'>Favorilerden Çıkar</button>
      <div class="puanlama">
        <span data-star="1">☆</span>
        <span data-star="2">☆</span>
        <span data-star="3">☆</span>
        <span data-star="4">☆</span>
        <span data-star="5">☆</span>
      </div>
    `;
    favoriAlan.appendChild(div);

    const puanlamaDiv = div.querySelector('.puanlama');
    puanGoster(film.imdbID, puanlamaDiv);
    puanlamaDiv.querySelectorAll('span').forEach(star => {
      star.onclick = (e) => {
        e.stopPropagation();
        const selectedPuan = Number(star.getAttribute('data-star'));
        puanla(film.imdbID, selectedPuan);
        puanGoster(film.imdbID, puanlamaDiv);
      };
    });
  });
}


function filmDetayGoster(imdbID) {
  fetch(`https://www.omdbapi.com/?apikey=${API_KEY}&i=${imdbID}&plot=full`)
    .then(res => res.json())
    .then(film => {
      if (film.Response === "True") {
        const detayAlani = document.getElementById('filmDetay');
        
        // Kullanıcı puanını getir
        const puanlar = JSON.parse(localStorage.getItem('puanlar')) || {};
        const mevcutPuan = puanlar[film.imdbID] || 0;

        // Kullanıcı yorumunu getir
        const yorumlar = JSON.parse(localStorage.getItem('yorumlar')) || {};
        const mevcutYorum = yorumlar[film.imdbID] || '';

        detayAlani.innerHTML = `
          <img src="${film.Poster !== "N/A" ? film.Poster : ''}" alt="Poster">
          <h2>${film.Title} (${film.Year})</h2>
          <p><strong>Tür:</strong> ${film.Genre}</p>
          <p><strong>Yönetmen:</strong> ${film.Director}</p>
          <p><strong>Oyuncular:</strong> ${film.Actors}</p>
          <p><strong>Özet:</strong> ${film.Plot}</p>
          <p><strong>IMDB Puanı:</strong> ${film.imdbRating}</p>

          <div class="puanlama-detay" style="margin-top: 10px;">Puan ver: </div>
          ${mevcutYorum ? `<div style="margin-top: 10px;"><strong>Senin Yorumun:</strong><br><em>${mevcutYorum}</em></div>` : ''}
        `;

        // Yıldızları ekle
        const puanDiv = detayAlani.querySelector('.puanlama-detay');
        for (let i = 1; i <= 5; i++) {
          const star = document.createElement('span');
          star.textContent = '★';
          star.style.fontSize = '22px';
          star.style.cursor = 'pointer';
          star.style.color = i <= mevcutPuan ? 'gold' : '#ccc';
          star.dataset.deger = i;
          puanDiv.appendChild(star);
        }

        const stars = puanDiv.querySelectorAll('span');
        stars.forEach((star, index) => {
          star.addEventListener('mouseover', () => {
            stars.forEach((s, idx) => {
              s.style.color = idx <= index ? 'gold' : '#ccc';
            });
          });
          star.addEventListener('mouseout', () => {
            stars.forEach((s, idx) => {
              s.style.color = idx < mevcutPuan ? 'gold' : '#ccc';
            });
          });
          star.addEventListener('click', () => {
            puanKaydet(film.imdbID, index + 1);
            yorumKutusuAc(film); // Yorum kutusu aç
          });
        });
      } else {
        alert("Detaylar alınamadı.");
      }
    });
}
function puanKaydet(imdbID, puan) {
  let puanlar = JSON.parse(localStorage.getItem('puanlar')) || {};
  puanlar[imdbID] = puan;
  localStorage.setItem('puanlar', JSON.stringify(puanlar));
  if (imdbID === aktifYorumFilmID) filmDetayGoster(imdbID);
}

let aktifYorumFilmID = null;

function yorumKutusuAc(film) {
  aktifYorumFilmID = film.imdbID;
  document.getElementById('yorumFilmBaslik').textContent = `Film: ${film.Title} (${film.Year})`;
  document.getElementById('yorumInput').value = '';
  document.getElementById('yorumKutusu').style.display = 'block';
}

function yorumKutusuKapat() {
  aktifYorumFilmID = null;
  document.getElementById('yorumKutusu').style.display = 'none';
}

function yorumKaydet() {
  const yorumMetni = document.getElementById('yorumInput').value.trim();
  if (!yorumMetni) {
    alert('Lütfen yorumunuzu yazın.');
    return;
  }
  let yorumlar = JSON.parse(localStorage.getItem('yorumlar')) || {};
  if (!yorumlar[aktifYorumFilmID]) yorumlar[aktifYorumFilmID] = [];
  yorumlar[aktifYorumFilmID].push(yorumMetni);
  localStorage.setItem('yorumlar', JSON.stringify(yorumlar));
  yorumKutusuKapat();
  if (aktifYorumFilmID) filmDetayGoster(aktifYorumFilmID);
}

   

    // Puanlama fonksiyonları
    function puanla(imdbID, puan) {
      let puanlar = JSON.parse(localStorage.getItem('puanlar')) || {};
      puanlar[imdbID] = puan;
      localStorage.setItem('puanlar', JSON.stringify(puanlar));
      favorileriGoster();
      filmAra(); // arama sonuçlarını da güncelle
    }

    function puanGoster(imdbID, container) {
      let puanlar = JSON.parse(localStorage.getItem('puanlar')) || {};
      const puan = puanlar[imdbID] || 0;
      const stars = container.querySelectorAll('span[data-star]');
      stars.forEach(star => {
        const starVal = Number(star.getAttribute('data-star'));
        if(starVal <= puan) star.classList.add('dolu');
        else star.classList.remove('dolu');
      });
    }

    // Tema değiştirme
    function temayiDegistir() {
      const mevcutTema = document.body.classList.contains('light') ? 'light' : 'dark';
      const yeniTema = mevcutTema === 'dark' ? 'light' : 'dark';
      document.body.classList.toggle('light', yeniTema === 'light');
      localStorage.setItem('tema', yeniTema);
      document.getElementById('temaButonu').textContent = yeniTema === 'light' ? '🌙' : '☀️';
    }

    // Sayfa yüklendiğinde
    window.onload = function() {
      const kayitliTema = localStorage.getItem('tema');
      if (kayitliTema === 'light') {
        document.body.classList.add('light');
        document.getElementById('temaButonu').textContent = '🌙';
      } else {
        document.getElementById('temaButonu').textContent = '☀️';
      }
      document.getElementById('aramaKutusu').addEventListener('keydown', function(e) {
        if (e.key === 'Enter') {
          filmAra();
        }
      });
      favorileriGoster();
    };</script>
</body>
</html>
