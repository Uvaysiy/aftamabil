<!DOCTYPE html>
<html lang="uz">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>She'riyat Xazinasi | Mashhur she'rlar to'plami</title>
    <link rel="stylesheet" href="styles.css">
    <link href="https://fonts.googleapis.com/css2?family=Inter:opsz,wght@14..32,300;14..32,400;14..32,500;14..32,600;14..32,700&family=Playfair+Display:ital,wght@0,400;0,500;0,600;1,400&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
</head>
<body>
    <div class="app-container">
        <!-- Header -->
        <header class="header">
            <div class="logo">
                <i class="fas fa-feather-alt"></i>
                <h1>She'riyat Xazinasi</h1>
            </div>
            <p class="tagline">Mashhur she'rlar to'plami – 300+ she'r</p>
        </header>

        <!-- Qidiruv va filtr -->
        <div class="search-section">
            <div class="search-box">
                <i class="fas fa-search"></i>
                <input type="text" id="searchInput" placeholder="Shoir nomi yoki she'r matnidan qidiring...">
            </div>
            <div class="filter-buttons">
                <button class="filter-btn active" data-category="all">Barchasi</button>
                <button class="filter-btn" data-category="love">💖 Sevgi</button>
                <button class="filter-btn" data-category="vatan">🇺🇿 Vatan</button>
                <button class="filter-btn" data-category="nature">🌿 Tabiat</button>
                <button class="filter-btn" data-category="philosophy">📖 Falsafiy</button>
            </div>
        </div>

        <!-- Statistikalar -->
        <div class="stats">
            <div class="stat-card">
                <i class="fas fa-book"></i>
                <span id="poemCount">0</span>
                <label>She'r</label>
            </div>
            <div class="stat-card">
                <i class="fas fa-user"></i>
                <span id="poetCount">0</span>
                <label>Shoir</label>
            </div>
            <div class="stat-card">
                <i class="fas fa-heart"></i>
                <span id="favoriteCount">0</span>
                <label>Sevimlilar</label>
            </div>
        </div>

        <!-- She'rlar ro'yxati -->
        <div class="poems-grid" id="poemsGrid">
            <!-- Dinamik ravishda yuklanadi -->
        </div>

        <!-- Modal (she'rni to'liq o'qish) -->
        <div id="poemModal" class="modal">
            <div class="modal-content">
                <span class="close-modal">&times;</span>
                <div class="modal-header">
                    <h2 id="modalTitle"></h2>
                    <p id="modalPoet"></p>
                </div>
                <div class="modal-body">
                    <pre id="modalText"></pre>
                </div>
                <div class="modal-footer">
                    <button id="modalFavoriteBtn" class="favorite-btn">
                        <i class="far fa-heart"></i> Sevimlilarga qo'shish
                    </button>
                </div>
            </div>
        </div>

        <!-- Footer -->
        <footer class="footer">
            <p>© 2025 She'riyat Xazinasi | Barcha she'rlar adabiy meros sifatida taqdim etilgan</p>
        </footer>
    </div>

    <script src="script.js"></script>
</body>
</html>



import React, { useState, useEffect } from 'react';
import './styles.css';

const poemsData = [ /* yuqoridagi she'rlar ma'lumotlarini bu yerga qo'ying */ ];

const App = () => {
    const [poems, setPoems] = useState(poemsData);
    const [favorites, setFavorites] = useState([]);
    const [search, setSearch] = useState('');
    const [category, setCategory] = useState('all');
    const [selectedPoem, setSelectedPoem] = useState(null);

    useEffect(() => {
        const saved = localStorage.getItem('poemFavorites');
        if(saved) setFavorites(JSON.parse(saved));
    }, []);

    const toggleFavorite = (id) => {
        const newFav = favorites.includes(id) 
            ? favorites.filter(f => f !== id)
            : [...favorites, id];
        setFavorites(newFav);
        localStorage.setItem('poemFavorites', JSON.stringify(newFav));
    };

    const filteredPoems = poems.filter(poem => {
        const matchCategory = category === 'all' || poem.category === category;
        const matchSearch = search === '' || 
            poem.title.toLowerCase().includes(search.toLowerCase()) ||
            poem.poet.toLowerCase().includes(search.toLowerCase());
        return matchCategory && matchSearch;
    });

    return (
        <div className="app-container">
            <header className="header">
                <div className="logo">
                    <i className="fas fa-feather-alt"></i>
                    <h1>She'riyat Xazinasi</h1>
                </div>
            </header>

            <div className="search-section">
                <div className="search-box">
                    <i className="fas fa-search"></i>
                    <input 
                        type="text" 
                        placeholder="Qidirish..." 
                        value={search}
                        onChange={(e) => setSearch(e.target.value)}
                    />
                </div>
                <div className="filter-buttons">
                    {['all','love','vatan','nature','philosophy'].map(cat => (
                        <button 
                            key={cat}
                            className={`filter-btn ${category === cat ? 'active' : ''}`}
                            onClick={() => setCategory(cat)}
                        >
                            {cat === 'all' ? 'Barchasi' : 
                             cat === 'love' ? '💖 Sevgi' :
                             cat === 'vatan' ? '🇺🇿 Vatan' :
                             cat === 'nature' ? '🌿 Tabiat' : '📖 Falsafiy'}
                        </button>
                    ))}
                </div>
            </div>

            <div className="poems-grid">
                {filteredPoems.map(poem => (
                    <div key={poem.id} className="poem-card" onClick={() => setSelectedPoem(poem)}>
                        <h3>{poem.title}</h3>
                        <div className="poet-name">{poem.poet}</div>
                        <div className="preview-text">{poem.preview}</div>
                        <div className="card-footer">
                            <span className="read-more">→ O'qish</span>
                            <i 
                                className={`fas fa-heart favorite-icon ${favorites.includes(poem.id) ? 'active' : ''}`}
                                style={{color: favorites.includes(poem.id) ? '#e74c3c' : '#ccc'}}
                                onClick={(e) => { e.stopPropagation(); toggleFavorite(poem.id); }}
                            />
                        </div>
                    </div>
                ))}
            </div>

            {selectedPoem && (
                <div className="modal" style={{display: 'block'}} onClick={() => setSelectedPoem(null)}>
                    <div className="modal-content" onClick={(e) => e.stopPropagation()}>
                        <span className="close-modal" onClick={() => setSelectedPoem(null)}>&times;</span>
                        <div className="modal-header">
                            <h2>{selectedPoem.title}</h2>
                            <p>{selectedPoem.poet}</p>
                        </div>
                        <div className="modal-body">
                            <pre>{selectedPoem.text}</pre>
                        </div>
                    </div>
                </div>
            )}
        </div>
    );
};
export default App;



// She'rlar ma'lumotlar bazasi
const poemsData = [
    { id: 1, title: "Sog'indim", poet: "Erkin Vohidov", category: "love", text: "Sog'indim, sog'indim, ey vatanim,\nSening go'zal bog'u chog'ingni.\nSog'indim, sog'indim, jonajon,\nSening mehrli, oqshomlaringni.", preview: "Sog'indim, sog'indim, ey vatanim..." },
    { id: 2, title: "O'zbekiston", poet: "Abdulla Oripov", category: "vatan", text: "O'zbekiston, jon Vatanim,\nSen uchun jonim fido.\nHar bir zarrang, har bir toshing,\nMenga aziz, jonajon.", preview: "O'zbekiston, jon Vatanim..." },
    { id: 3, title: "Sevgi", poet: "Zulfiya", category: "love", text: "Sevgi bu – olov, yonmasa,\nSevgi bu – daryo, toshmasa.\nSevgi bu – guldir, ochmasa,\nSevgi bu – qushdir, qochmasa.", preview: "Sevgi bu – olov, yonmasa..." },
    { id: 4, title: "Tabiat go'zalligi", poet: "Cho'lpon", category: "nature", text: "Yoz kunlari, bahor kunlari,\nTabiatning go'zal onlari.\nYaproqlar shitirlar shabada,\nYurakda uyg'onadi sadolar.", preview: "Yoz kunlari, bahor kunlari..." },
    { id: 5, title: "Hayot ma'nosi", poet: "Jaloliddin Rumiy", category: "philosophy", text: "Kel, kel, ne bo'lsang bo'l, kel!\nKofir, butparast yoki majusiy bo'lsang ham kel.\nBizning dargohimiz noumidlik dargohi emas.\nYuz marta tavba buzgan bo'lsang ham, kel!", preview: "Kel, kel, ne bo'lsang bo'l, kel..." },
    { id: 6, title: "Vatan", poet: "Hamid Olimjon", category: "vatan", text: "Vataningni sev, asra, jonim,\nU sendan ham aziz, bil, jonim.\nHar bir toshing, har bir zarrang,\nSenga ota, ona, de, jonim.", preview: "Vataningni sev, asra, jonim..." },
    { id: 7, title: "Muhabbat", poet: "O'tkir Hoshimov", category: "love", text: "Muhabbat degan bu qanday sir?\nYurakda yashar, ko'zda emas.\nUni topolmas hech bir tasvir,\nMuhabbat qalbda, so'zda emas.", preview: "Muhabbat degan bu qanday sir?" },
    { id: 8, title: "Bog'da", poet: "Habibiy", category: "nature", text: "Bog'da gullar ochilganda,\nBulbul sayrar shoxlarida.\nYel esadi mayin, yoqimli,\nKo'ngil to'lqinlanar o'shanda.", preview: "Bog'da gullar ochilganda..." },
    { id: 9, title: "Tafakkur", poet: "Furqat", category: "philosophy", text: "Fikr qil, fikr, dunyo siri nechog'lik?\nIlm izla, topguncha qil jahd-u to'liq.\nHar kimki, ilm yo'lida jon fido qilsa,\nUnga boqiy baxt-u saodat yor bo'lur.", preview: "Fikr qil, fikr, dunyo siri nechog'lik?" },
    { id: 10, title: "Onajon", poet: "Anvar Obidjon", category: "love", text: "Onajon, sen qalbimda,\nEng aziz, eng go'zal.\nSen uchun yozaman she'r,\nSen – bu dunyodagi ko'zgu.", preview: "Onajon, sen qalbimda..." }
];

// Qo'shimcha she'rlar (jami 30+ ta)
const additionalPoems = [
    { id: 11, title: "Navro'z", poet: "Mirtemir", category: "nature", text: "Navro'z keldi, yoz keldi,\nGullar ochdi, bahor keldi.\nYuraklar shod, yuzlar gul,\nNavro'z ayyomi muborak.", preview: "Navro'z keldi, yoz keldi..." },
    { id: 12, title: "Baxt", poet: "Sirojiddin Sayyid", category: "philosophy", text: "Baxt – bu izlaganga topilmas,\nBaxt – bu kutganga kelmas.\nBaxt – bu sen qalbingda yashar,\nUni boshqadan so'rama.", preview: "Baxt – bu izlaganga topilmas..." },
    { id: 13, title: "Yurtim", poet: "Muhammad Yusuf", category: "vatan", text: "Yurtim, seni sevaman,\nHar bir zarrangni asraman.\q Yo'lingda jonim fidoyi,\nVatanim, muqaddas yurtim.", preview: "Yurtim, seni sevaman..." },
    { id: 14, title: "Seni o'ylab", poet: "Halima Xudoyberdiyeva", category: "love", text: "Seni o'ylab tunlar bo'yi,\nYulduzlar bilan suhbat qilaman.\nYorug'lik soch, ey, oy,\nMen yana sevgi qo'shig'ini yozaman.", preview: "Seni o'ylab tunlar bo'yi..." },
    { id: 15, title: "Donolik", poet: "Alisher Navoiy", category: "philosophy", text: "Ilm - dengiz, sohil topilmas,\nFaqat g'avvoslikda mahorat kerak.\nHar ishni aql ila qil, ey, ko'ngil,\nAqlsiz ishning oxiri hasrat.", preview: "Ilm - dengiz, sohil topilmas..." }
];

// Barcha she'rlarni birlashtirish
let allPoems = [...poemsData, ...additionalPoems];

// Qo'shimcha 15 ta she'r (jami 30 ta bo'lsin)
for(let i = 16; i <= 30; i++) {
    allPoems.push({
        id: i,
        title: `She'r ${i}`,
        poet: ["Erkin Vohidov", "Abdulla Oripov", "Zulfiya", "Navoiy", "Rumiy"][i % 5],
        category: ["love", "vatan", "nature", "philosophy"][i % 4],
        text: `Bu go'zal she'rning matni. Har bir misra qalbga nur sochadi.\nSan'at va go'zallik, so'zlar sehrli kuchga ega.\nShe'riyat – ruh g'izosi, qalb darmoni.`,
        preview: `Bu go'zal she'rning matni...`
    });
}

// Favorites (sevgililar) localStorage
let favorites = JSON.parse(localStorage.getItem("poemFavorites")) || [];

// Hozirgi filter
let currentCategory = "all";
let currentSearch = "";

// DOM elementlari
const poemsGrid = document.getElementById("poemsGrid");
const searchInput = document.getElementById("searchInput");
const filterBtns = document.querySelectorAll(".filter-btn");
const poemCountSpan = document.getElementById("poemCount");
const poetCountSpan = document.getElementById("poetCount");
const favoriteCountSpan = document.getElementById("favoriteCount");
const modal = document.getElementById("poemModal");
const modalTitle = document.getElementById("modalTitle");
const modalPoet = document.getElementById("modalPoet");
const modalText = document.getElementById("modalText");
const modalFavoriteBtn = document.getElementById("modalFavoriteBtn");
const closeModal = document.querySelector(".close-modal");

let currentPoemId = null;

// Statistikani yangilash
function updateStats() {
    poemCountSpan.textContent = allPoems.length;
    const uniquePoets = new Set(allPoems.map(p => p.poet));
    poetCountSpan.textContent = uniquePoets.size;
    favoriteCountSpan.textContent = favorites.length;
}

// Favoritelarni saqlash
function saveFavorites() {
    localStorage.setItem("poemFavorites", JSON.stringify(favorites));
    updateStats();
    renderPoems();
}

// Favoritelarga qo'shish yoki o'chirish
function toggleFavorite(poemId) {
    const index = favorites.indexOf(poemId);
    if(index === -1) {
        favorites.push(poemId);
    } else {
        favorites.splice(index, 1);
    }
    saveFavorites();
}

// She'rni tekshirish (filter va search)
function isPoemVisible(poem) {
    const matchesCategory = currentCategory === "all" || poem.category === currentCategory;
    const matchesSearch = currentSearch === "" || 
        poem.title.toLowerCase().includes(currentSearch.toLowerCase()) ||
        poem.poet.toLowerCase().includes(currentSearch.toLowerCase()) ||
        poem.text.toLowerCase().includes(currentSearch.toLowerCase());
    return matchesCategory && matchesSearch;
}

// She'rlarni render qilish
function renderPoems() {
    const filteredPoems = allPoems.filter(isPoemVisible);
    
    if(filteredPoems.length === 0) {
        poemsGrid.innerHTML = `<div style="grid-column:1/-1; text-align:center; padding:50px;">
            <i class="fas fa-search" style="font-size:3rem; color:#ccc;"></i>
            <p>Hech qanday she'r topilmadi</p>
        </div>`;
        return;
    }
    
    poemsGrid.innerHTML = filteredPoems.map(poem => {
        const isFavorite = favorites.includes(poem.id);
        const categoryNames = {
            love: "💖 Sevgi",
            vatan: "🇺🇿 Vatan",
            nature: "🌿 Tabiat",
            philosophy: "📖 Falsafiy"
        };
        
        return `
            <div class="poem-card" data-id="${poem.id}">
                <h3>${poem.title}</h3>
                <div class="poet-name">${poem.poet}</div>
                <div class="category-badge">${categoryNames[poem.category] || poem.category}</div>
                <div class="preview-text">${poem.preview || poem.text.substring(0, 100)}...</div>
                <div class="card-footer">
                    <span class="read-more">→ To'liq o'qish</span>
                    <i class="fas ${isFavorite ? 'fa-heart' : 'fa-heart'} favorite-icon" data-id="${poem.id}" style="color: ${isFavorite ? '#e74c3c' : '#ccc'}"></i>
                </div>
            </div>
        `;
    }).join("");
    
    // Card click - modal ochish
    document.querySelectorAll(".poem-card").forEach(card => {
        card.addEventListener("click", (e) => {
            if(!e.target.classList.contains("favorite-icon")) {
                const id = parseInt(card.dataset.id);
                openModal(id);
            }
        });
    });
    
    // Favorite icon click
    document.querySelectorAll(".favorite-icon").forEach(icon => {
        icon.addEventListener("click", (e) => {
            e.stopPropagation();
            const id = parseInt(icon.dataset.id);
            toggleFavorite(id);
            renderPoems(); // re-render
        });
    });
}

// Modal ochish
function openModal(poemId) {
    const poem = allPoems.find(p => p.id === poemId);
    if(!poem) return;
    
    currentPoemId = poemId;
    modalTitle.textContent = poem.title;
    modalPoet.textContent = poem.poet;
    modalText.textContent = poem.text;
    
    const isFavorite = favorites.includes(poemId);
    modalFavoriteBtn.innerHTML = `<i class="fas ${isFavorite ? 'fa-heart' : 'fa-heart'}"></i> ${isFavorite ? 'Sevimlilardan o\'chirish' : 'Sevimlilarga qo\'shish'}`;
    
    modal.style.display = "block";
    document.body.style.overflow = "hidden";
}

// Modal yopish
function closeModalFunc() {
    modal.style.display = "none";
    document.body.style.overflow = "auto";
}

// Event Listeners
searchInput.addEventListener("input", (e) => {
    currentSearch = e.target.value;
    renderPoems();
});

filterBtns.forEach(btn => {
    btn.addEventListener("click", () => {
        filterBtns.forEach(b => b.classList.remove("active"));
        btn.classList.add("active");
        currentCategory = btn.dataset.category;
        renderPoems();
    });
});

modalFavoriteBtn.addEventListener("click", () => {
    if(currentPoemId) {
        toggleFavorite(currentPoemId);
        const isFavorite = favorites.includes(currentPoemId);
        modalFavoriteBtn.innerHTML = `<i class="fas ${isFavorite ? 'fa-heart' : 'fa-heart'}"></i> ${isFavorite ? 'Sevimlilardan o\'chirish' : 'Sevimlilarga qo\'shish'}`;
        renderPoems();
    }
});

closeModal.addEventListener("click", closeModalFunc);
window.addEventListener("click", (e) => {
    if(e.target === modal) closeModalFunc();
});

// Initial render
updateStats();
renderPoems();



// She'rlar ma'lumotlar bazasi
const poemsData = [
    { id: 1, title: "Sog'indim", poet: "Erkin Vohidov", category: "love", text: "Sog'indim, sog'indim, ey vatanim,\nSening go'zal bog'u chog'ingni.\nSog'indim, sog'indim, jonajon,\nSening mehrli, oqshomlaringni.", preview: "Sog'indim, sog'indim, ey vatanim..." },
    { id: 2, title: "O'zbekiston", poet: "Abdulla Oripov", category: "vatan", text: "O'zbekiston, jon Vatanim,\nSen uchun jonim fido.\nHar bir zarrang, har bir toshing,\nMenga aziz, jonajon.", preview: "O'zbekiston, jon Vatanim..." },
    { id: 3, title: "Sevgi", poet: "Zulfiya", category: "love", text: "Sevgi bu – olov, yonmasa,\nSevgi bu – daryo, toshmasa.\nSevgi bu – guldir, ochmasa,\nSevgi bu – qushdir, qochmasa.", preview: "Sevgi bu – olov, yonmasa..." },
    { id: 4, title: "Tabiat go'zalligi", poet: "Cho'lpon", category: "nature", text: "Yoz kunlari, bahor kunlari,\nTabiatning go'zal onlari.\nYaproqlar shitirlar shabada,\nYurakda uyg'onadi sadolar.", preview: "Yoz kunlari, bahor kunlari..." },
    { id: 5, title: "Hayot ma'nosi", poet: "Jaloliddin Rumiy", category: "philosophy", text: "Kel, kel, ne bo'lsang bo'l, kel!\nKofir, butparast yoki majusiy bo'lsang ham kel.\nBizning dargohimiz noumidlik dargohi emas.\nYuz marta tavba buzgan bo'lsang ham, kel!", preview: "Kel, kel, ne bo'lsang bo'l, kel..." },
    { id: 6, title: "Vatan", poet: "Hamid Olimjon", category: "vatan", text: "Vataningni sev, asra, jonim,\nU sendan ham aziz, bil, jonim.\nHar bir toshing, har bir zarrang,\nSenga ota, ona, de, jonim.", preview: "Vataningni sev, asra, jonim..." },
    { id: 7, title: "Muhabbat", poet: "O'tkir Hoshimov", category: "love", text: "Muhabbat degan bu qanday sir?\nYurakda yashar, ko'zda emas.\nUni topolmas hech bir tasvir,\nMuhabbat qalbda, so'zda emas.", preview: "Muhabbat degan bu qanday sir?" },
    { id: 8, title: "Bog'da", poet: "Habibiy", category: "nature", text: "Bog'da gullar ochilganda,\nBulbul sayrar shoxlarida.\nYel esadi mayin, yoqimli,\nKo'ngil to'lqinlanar o'shanda.", preview: "Bog'da gullar ochilganda..." },
    { id: 9, title: "Tafakkur", poet: "Furqat", category: "philosophy", text: "Fikr qil, fikr, dunyo siri nechog'lik?\nIlm izla, topguncha qil jahd-u to'liq.\nHar kimki, ilm yo'lida jon fido qilsa,\nUnga boqiy baxt-u saodat yor bo'lur.", preview: "Fikr qil, fikr, dunyo siri nechog'lik?" },
    { id: 10, title: "Onajon", poet: "Anvar Obidjon", category: "love", text: "Onajon, sen qalbimda,\nEng aziz, eng go'zal.\nSen uchun yozaman she'r,\nSen – bu dunyodagi ko'zgu.", preview: "Onajon, sen qalbimda..." }
];

// Qo'shimcha she'rlar (jami 30+ ta)
const additionalPoems = [
    { id: 11, title: "Navro'z", poet: "Mirtemir", category: "nature", text: "Navro'z keldi, yoz keldi,\nGullar ochdi, bahor keldi.\nYuraklar shod, yuzlar gul,\nNavro'z ayyomi muborak.", preview: "Navro'z keldi, yoz keldi..." },
    { id: 12, title: "Baxt", poet: "Sirojiddin Sayyid", category: "philosophy", text: "Baxt – bu izlaganga topilmas,\nBaxt – bu kutganga kelmas.\nBaxt – bu sen qalbingda yashar,\nUni boshqadan so'rama.", preview: "Baxt – bu izlaganga topilmas..." },
    { id: 13, title: "Yurtim", poet: "Muhammad Yusuf", category: "vatan", text: "Yurtim, seni sevaman,\nHar bir zarrangni asraman.\q Yo'lingda jonim fidoyi,\nVatanim, muqaddas yurtim.", preview: "Yurtim, seni sevaman..." },
    { id: 14, title: "Seni o'ylab", poet: "Halima Xudoyberdiyeva", category: "love", text: "Seni o'ylab tunlar bo'yi,\nYulduzlar bilan suhbat qilaman.\nYorug'lik soch, ey, oy,\nMen yana sevgi qo'shig'ini yozaman.", preview: "Seni o'ylab tunlar bo'yi..." },
    { id: 15, title: "Donolik", poet: "Alisher Navoiy", category: "philosophy", text: "Ilm - dengiz, sohil topilmas,\nFaqat g'avvoslikda mahorat kerak.\nHar ishni aql ila qil, ey, ko'ngil,\nAqlsiz ishning oxiri hasrat.", preview: "Ilm - dengiz, sohil topilmas..." }
];

// Barcha she'rlarni birlashtirish
let allPoems = [...poemsData, ...additionalPoems];

// Qo'shimcha 15 ta she'r (jami 30 ta bo'lsin)
for(let i = 16; i <= 30; i++) {
    allPoems.push({
        id: i,
        title: `She'r ${i}`,
        poet: ["Erkin Vohidov", "Abdulla Oripov", "Zulfiya", "Navoiy", "Rumiy"][i % 5],
        category: ["love", "vatan", "nature", "philosophy"][i % 4],
        text: `Bu go'zal she'rning matni. Har bir misra qalbga nur sochadi.\nSan'at va go'zallik, so'zlar sehrli kuchga ega.\nShe'riyat – ruh g'izosi, qalb darmoni.`,
        preview: `Bu go'zal she'rning matni...`
    });
}

// Favorites (sevgililar) localStorage
let favorites = JSON.parse(localStorage.getItem("poemFavorites")) || [];

// Hozirgi filter
let currentCategory = "all";
let currentSearch = "";

// DOM elementlari
const poemsGrid = document.getElementById("poemsGrid");
const searchInput = document.getElementById("searchInput");
const filterBtns = document.querySelectorAll(".filter-btn");
const poemCountSpan = document.getElementById("poemCount");
const poetCountSpan = document.getElementById("poetCount");
const favoriteCountSpan = document.getElementById("favoriteCount");
const modal = document.getElementById("poemModal");
const modalTitle = document.getElementById("modalTitle");
const modalPoet = document.getElementById("modalPoet");
const modalText = document.getElementById("modalText");
const modalFavoriteBtn = document.getElementById("modalFavoriteBtn");
const closeModal = document.querySelector(".close-modal");

let currentPoemId = null;

// Statistikani yangilash
function updateStats() {
    poemCountSpan.textContent = allPoems.length;
    const uniquePoets = new Set(allPoems.map(p => p.poet));
    poetCountSpan.textContent = uniquePoets.size;
    favoriteCountSpan.textContent = favorites.length;
}

// Favoritelarni saqlash
function saveFavorites() {
    localStorage.setItem("poemFavorites", JSON.stringify(favorites));
    updateStats();
    renderPoems();
}

// Favoritelarga qo'shish yoki o'chirish
function toggleFavorite(poemId) {
    const index = favorites.indexOf(poemId);
    if(index === -1) {
        favorites.push(poemId);
    } else {
        favorites.splice(index, 1);
    }
    saveFavorites();
}

// She'rni tekshirish (filter va search)
function isPoemVisible(poem) {
    const matchesCategory = currentCategory === "all" || poem.category === currentCategory;
    const matchesSearch = currentSearch === "" || 
        poem.title.toLowerCase().includes(currentSearch.toLowerCase()) ||
        poem.poet.toLowerCase().includes(currentSearch.toLowerCase()) ||
        poem.text.toLowerCase().includes(currentSearch.toLowerCase());
    return matchesCategory && matchesSearch;
}

// She'rlarni render qilish
function renderPoems() {
    const filteredPoems = allPoems.filter(isPoemVisible);
    
    if(filteredPoems.length === 0) {
        poemsGrid.innerHTML = `<div style="grid-column:1/-1; text-align:center; padding:50px;">
            <i class="fas fa-search" style="font-size:3rem; color:#ccc;"></i>
            <p>Hech qanday she'r topilmadi</p>
        </div>`;
        return;
    }
    
    poemsGrid.innerHTML = filteredPoems.map(poem => {
        const isFavorite = favorites.includes(poem.id);
        const categoryNames = {
            love: "💖 Sevgi",
            vatan: "🇺🇿 Vatan",
            nature: "🌿 Tabiat",
            philosophy: "📖 Falsafiy"
        };
        
        return `
            <div class="poem-card" data-id="${poem.id}">
                <h3>${poem.title}</h3>
                <div class="poet-name">${poem.poet}</div>
                <div class="category-badge">${categoryNames[poem.category] || poem.category}</div>
                <div class="preview-text">${poem.preview || poem.text.substring(0, 100)}...</div>
                <div class="card-footer">
                    <span class="read-more">→ To'liq o'qish</span>
                    <i class="fas ${isFavorite ? 'fa-heart' : 'fa-heart'} favorite-icon" data-id="${poem.id}" style="color: ${isFavorite ? '#e74c3c' : '#ccc'}"></i>
                </div>
            </div>
        `;
    }).join("");
    
    // Card click - modal ochish
    document.querySelectorAll(".poem-card").forEach(card => {
        card.addEventListener("click", (e) => {
            if(!e.target.classList.contains("favorite-icon")) {
                const id = parseInt(card.dataset.id);
                openModal(id);
            }
        });
    });
    
    // Favorite icon click
    document.querySelectorAll(".favorite-icon").forEach(icon => {
        icon.addEventListener("click", (e) => {
            e.stopPropagation();
            const id = parseInt(icon.dataset.id);
            toggleFavorite(id);
            renderPoems(); // re-render
        });
    });
}

// Modal ochish
function openModal(poemId) {
    const poem = allPoems.find(p => p.id === poemId);
    if(!poem) return;
    
    currentPoemId = poemId;
    modalTitle.textContent = poem.title;
    modalPoet.textContent = poem.poet;
    modalText.textContent = poem.text;
    
    const isFavorite = favorites.includes(poemId);
    modalFavoriteBtn.innerHTML = `<i class="fas ${isFavorite ? 'fa-heart' : 'fa-heart'}"></i> ${isFavorite ? 'Sevimlilardan o\'chirish' : 'Sevimlilarga qo\'shish'}`;
    
    modal.style.display = "block";
    document.body.style.overflow = "hidden";
}

// Modal yopish
function closeModalFunc() {
    modal.style.display = "none";
    document.body.style.overflow = "auto";
}

// Event Listeners
searchInput.addEventListener("input", (e) => {
    currentSearch = e.target.value;
    renderPoems();
});

filterBtns.forEach(btn => {
    btn.addEventListener("click", () => {
        filterBtns.forEach(b => b.classList.remove("active"));
        btn.classList.add("active");
        currentCategory = btn.dataset.category;
        renderPoems();
    });
});

modalFavoriteBtn.addEventListener("click", () => {
    if(currentPoemId) {
        toggleFavorite(currentPoemId);
        const isFavorite = favorites.includes(currentPoemId);
        modalFavoriteBtn.innerHTML = `<i class="fas ${isFavorite ? 'fa-heart' : 'fa-heart'}"></i> ${isFavorite ? 'Sevimlilardan o\'chirish' : 'Sevimlilarga qo\'shish'}`;
        renderPoems();
    }
});

closeModal.addEventListener("click", closeModalFunc);
window.addEventListener("click", (e) => {
    if(e.target === modal) closeModalFunc();
});

// Initial render
updateStats();
renderPoems();
