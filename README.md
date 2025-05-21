<!DOCTYPE html>
<html lang="tr">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Animezium</title>
  <link href="https://cdn.jsdelivr.net/npm/tailwindcss@2.2.19/dist/tailwind.min.css" rel="stylesheet">
</head>
<body class="bg-gray-900 text-white font-sans" id="theme">
  <header class="bg-gray-800 p-4 shadow">
    <div class="container mx-auto flex justify-between items-center">
      <h1 class="text-3xl font-bold text-purple-400">Animezium</h1>
      <nav class="space-x-4">
        <a href="#" class="hover:text-purple-300">Ana Sayfa</a>
        <a href="#" class="hover:text-purple-300">Popüler</a>
        <a href="#" class="hover:text-purple-300">İzle</a>
        <a href="#" onclick="showFavorites()" class="hover:text-purple-300">Favoriler</a>
        <a href="#" onclick="showProfile()" class="hover:text-purple-300">Profil</a>
        <a href="#" onclick="showHistory()" class="hover:text-purple-300">Geçmiş</a>
        <a href="#" onclick="showLogin()" class="hover:text-purple-300">Giriş</a>
        <a href="#" onclick="showRegister()" class="hover:text-purple-300">Kayıt</a>
        <a href="#" onclick="showAdmin()" class="hover:text-purple-300">Yönetim</a>
        <button onclick="toggleTheme()" class="ml-4 text-sm text-gray-300 border px-2 py-1 rounded">🌙/☀️</button>
      </nav>
    </div>
  </header>

  <main class="container mx-auto px-4 py-8">
    <section id="login" class="hidden">
      <h2 class="text-2xl font-bold mb-4">Giriş Yap</h2>
      <input id="username" placeholder="Kullanıcı Adı" class="mb-2 p-2 w-full bg-gray-700 rounded" />
      <input id="password" type="password" placeholder="Şifre" class="mb-4 p-2 w-full bg-gray-700 rounded" />
      <button onclick="loginUser()" class="bg-purple-600 hover:bg-purple-700 px-4 py-2 rounded">Giriş</button>
      <p id="login-status" class="mt-2 text-sm"></p>
    </section>

    <section id="register" class="hidden">
      <h2 class="text-2xl font-bold mb-4">Kayıt Ol</h2>
      <input id="newUser" placeholder="Yeni Kullanıcı Adı" class="mb-2 p-2 w-full bg-gray-700 rounded" />
      <input id="newPass" type="password" placeholder="Yeni Şifre" class="mb-4 p-2 w-full bg-gray-700 rounded" />
      <button onclick="registerUser()" class="bg-purple-600 hover:bg-purple-700 px-4 py-2 rounded">Kayıt Ol</button>
      <p id="register-status" class="mt-2 text-sm"></p>
    </section>

    <section id="admin-panel" class="hidden mt-10">
      <h2 class="text-2xl font-bold mb-4">Yönetici Paneli</h2>
      <p>Yorumları sil, kullanıcıları yönet vb. Özellikler gelecektir.</p>
    </section>

    <section id="history" class="hidden mt-10">
      <h3 class="text-xl font-semibold mb-4">İzleme Geçmişi</h3>
      <div id="history-list" class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 gap-6"></div>
    </section>

    <section id="comments" class="mt-10">
      <h2 class="text-2xl font-bold mb-4">Yorumlar</h2>
      <div id="comment-list" class="space-y-2"></div>
      <textarea id="new-comment" placeholder="Yorumunuzu yazın..." class="w-full p-2 rounded bg-gray-700"></textarea>
      <button onclick="postComment()" class="mt-2 bg-purple-600 px-4 py-2 rounded hover:bg-purple-700">Gönder</button>
    </section>
  </main>

  <footer class="bg-gray-800 p-4 text-center text-sm text-gray-400">
    © 2025 Animezium. Tüm hakları saklıdır.
  </footer>

  <script>
    let favorites = JSON.parse(localStorage.getItem("animeziumFavs")) || [];
    let history = JSON.parse(localStorage.getItem("animeziumHistory")) || [];
    let currentUser = localStorage.getItem("animeziumUser") || null;
    let users = JSON.parse(localStorage.getItem("animeziumUsers")) || [];
    let comments = JSON.parse(localStorage.getItem("animeziumComments")) || [];

    function showRegister() {
      hideAllSections();
      document.getElementById("register").classList.remove("hidden");
    }

    function registerUser() {
      const newUser = document.getElementById("newUser").value;
      const newPass = document.getElementById("newPass").value;
      if (users.find(u => u.username === newUser)) {
        document.getElementById("register-status").innerText = "Kullanıcı adı zaten mevcut!";
      } else {
        users.push({ username: newUser, password: newPass });
        localStorage.setItem("animeziumUsers", JSON.stringify(users));
        document.getElementById("register-status").innerText = "Kayıt başarılı!";
      }
    }

    function postComment() {
      const commentText = document.getElementById("new-comment").value;
      if (!currentUser || !commentText.trim()) return;
      const comment = { user: currentUser, text: commentText, date: new Date().toLocaleString() };
      comments.push(comment);
      localStorage.setItem("animeziumComments", JSON.stringify(comments));
      renderComments();
      document.getElementById("new-comment").value = "";
    }

    function renderComments() {
      const list = document.getElementById("comment-list");
      if (!list) return;
      list.innerHTML = "";
      comments.forEach(c => {
        list.innerHTML += `<div class='bg-gray-800 p-2 rounded'><strong>${c.user}</strong>: ${c.text} <small class='text-gray-400'>(${c.date})</small></div>`;
      });
    }

    function loginUser() {
      const user = document.getElementById("username").value;
      const pass = document.getElementById("password").value;
      if (user === "admin" && pass === "admin") {
        currentUser = user;
        localStorage.setItem("animeziumUser", user);
        document.getElementById("login-status").innerText = "Giriş başarılı (Admin).";
        showAdmin();
      } else if (users.find(u => u.username === user && u.password === pass)) {
        currentUser = user;
        localStorage.setItem("animeziumUser", user);
        document.getElementById("login-status").innerText = `Hoş geldin, ${user}`;
      } else {
        document.getElementById("login-status").innerText = "Hatalı giriş!";
      }
    }

    function hideAllSections() {
      ["login", "register", "admin-panel", "history", "favorites", "profile"].forEach(id => {
        const el = document.getElementById(id);
        if (el) el.classList.add("hidden");
      });
    }

    function showAdmin() {
      if (currentUser === "admin") {
        hideAllSections();
        document.getElementById("admin-panel").classList.remove("hidden");
      } else {
        alert("Yalnızca admin erişebilir!");
      }
    }

    function showLogin() {
      hideAllSections();
      document.getElementById("login").classList.remove("hidden");
    }

    function showHistory() {
      hideAllSections();
      document.getElementById("history").classList.remove("hidden");
      const container = document.getElementById("history-list");
      container.innerHTML = "";
      history.forEach(anime => {
        container.innerHTML += `
          <div class="bg-gray-800 rounded-lg shadow p-4">
            <img src="${anime.images.jpg.image_url}" alt="${anime.title}" class="rounded mb-2">
            <h3 class="text-xl font-bold">${anime.title}</h3>
            <a href="${anime.url}" target="_blank" class="text-purple-400 hover:underline">İzle</a>
          </div>`;
      });
    }

    function toggleTheme() {
      const body = document.getElementById("theme");
      body.classList.toggle("bg-gray-900");
      body.classList.toggle("bg-white");
      body.classList.toggle("text-white");
      body.classList.toggle("text-black");
    }

    function showFavorites() {
      alert("Favoriler bölümü yakında geliyor!");
    }

    function showProfile() {
      alert("Profil bölümü yakında geliyor!");
    }

    renderComments();
  </script>
</body>
</html>
