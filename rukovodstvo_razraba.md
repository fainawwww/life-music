 markdown
# Проект: «Life Music» — музыкальная библиотека с плейлистами

## Общая информация

Проект представляет собой **frontend-приложение** для управления музыкальной коллекцией, написанное на:

- **HTML**
- **CSS**
- **Vanilla JavaScript**

Архитектура проекта простая и не использует backend или сторонние библиотеки. Все данные хранятся в **localStorage** браузера.

---

## Структура проекта
life-music/
│
├── index.html # Главный файл приложения (вся разметка, стили и скрипты)
├── README.md # Документация проекта
├── rukovodstvo_razraba.md # Руководство разработчика
│
└── (нет внешних папок — все данные хранятся в браузере)

text

> ℹ️ Проект является **одностраничным приложением (SPA)**. Весь код содержится в одном файле `index.html`.

---

## Архитектура проекта

Проект состоит из **трёх основных разделов** (страниц), которые переключаются динамически без перезагрузки:

| Раздел | ID страницы | Назначение |
|--------|-------------|------------|
| Мои треки | `playerPage` | Отображение библиотеки треков с плеером |
| Мои плейлисты | `playlistsPage` | Создание и управление плейлистами |
| Добавить трек | `addPage` | Загрузка новых аудиофайлов с обложками |

---

## Работа с треками

Все треки хранятся в массиве:

```javascript
let tracks = [];

// Структура одного трека:
{
    id: number,              // Уникальный идентификатор
    name: string,            // Название трека
    fileDataBase64: string,  // Аудиоданные в формате Base64
    mimeType: string,        // Тип файла (audio/mpeg и т.д.)
    coverBase64: string,     // Обложка в Base64 (или null)
    size: number,            // Размер файла в байтах
    fileName: string,        // Оригинальное имя файла
    dateAdded: string        // Дата добавления (ISO строка)
}
Добавление нового трека
Чтобы добавить трек программно (без интерфейса), используйте функцию:

javascript
await addTrack(audioFile, coverFile, customName);
Параметры:

audioFile — объект File (обязательно)

coverFile — объект File или null (опционально)

customName — строка с названием (опционально)

Пример:

javascript
const audioInput = document.querySelector('#audioFileInput');
const coverInput = document.querySelector('#coverFileInput');
await addTrack(audioInput.files[0], coverInput.files[0], "Мой любимый трек");
Удаление трека
javascript
deleteTrackById(trackId);
Автоматически удаляет трек из всех плейлистов.

Работа с плейлистами
Все плейлисты хранятся в массиве:

javascript
let playlists = [];

// Структура одного плейлиста:
{
    id: number,              // Уникальный идентификатор
    name: string,            // Название плейлиста
    coverBase64: string,     // Обложка в Base64 (или null)
    trackIds: number[]       // Массив ID треков
}
Создание нового плейлиста
Через интерфейс: нажмите кнопку «+ Новый плейлист» на вкладке «Мои плейлисты».

Программно:

javascript
const newPlaylist = {
    id: Date.now() + Math.random() * 10000,
    name: "Мой плейлист",
    coverBase64: null,
    trackIds: []
};
playlists.push(newPlaylist);
savePlaylists();
renderPlaylists();
Добавление трека в плейлист
javascript
const playlist = playlists.find(p => p.id === playlistId);
if (playlist && !playlist.trackIds.includes(trackId)) {
    playlist.trackIds.push(trackId);
    savePlaylists();
}
🗄️ Система хранения данных
Все данные сохраняются в localStorage:

Ключ	Содержание
life_music_tracks_v2	JSON-строка массива tracks
life_music_playlists_v2	JSON-строка массива playlists
Основные функции работы с хранилищем
javascript
loadData()       // Загружает треки и плейлисты из localStorage
saveTracks()     // Сохраняет массив tracks
savePlaylists()  // Сохраняет массив playlists
🔧 Основные функции
Работа с файлами
javascript
fileToBase64(file)
Преобразует файл в Base64-строку (используется FileReader).

Пример:

javascript
const base64 = await fileToBase64(audioFile);
Рендеринг интерфейса
javascript
renderLibrary()      // Отрисовывает сетку треков на странице «Мои треки»
renderPlaylists()    // Отрисовывает сетку плейлистов
Навигация
javascript
setActivePage(pageId)  // Переключает видимую страницу (player/playlists/add)
Модальные окна
javascript
showAddToPlaylistModal(trackId)   // Модалка для добавления трека в плейлист
showPlaylistDetail(playlistId)    // Показывает содержимое плейлиста
openMoodModal(moodKey)            // Открывает mood-подборку
Вспомогательные функции
javascript
escapeHtml(str)  // Защита от XSS — экранирует спецсимволы
🎨 Система mood-подборок
Подборки хранятся в объекте moodData:

javascript
const moodData = {
    recommendations: {
        title: "⭐ Наши рекомендации 2026",
        coverPlaceholder: "🎵 ТОП-2026",
        tracks: [
            { name: "Neon Skies", artist: "Echo Valley & Luna Wave" },
            { name: "Midnight Drive", artist: "The Voidwalkers" }
        ]
    },
    calm: { ... },
    productive: { ... },
    morning: { ... }
};
Добавление новой mood-подборки
Добавьте новый ключ в объект moodData

Добавьте кнопку в HTML:

html
<button class="mood-btn" data-mood="workout">💪 Тренировка</button>
Добавьте условие в обработчик:

javascript
else if(mood === 'workout') openMoodModal('workout');
🧩 Работа с обложками
Обложки хранятся в формате Base64 и могут быть:

у треков (coverBase64)

у плейлистов (coverBase64)

у mood-подборок (пользователь может заменить стандартную SVG)

Пример отображения обложки:

javascript
const coverImg = track.coverBase64 
    ? `<img class="track-cover" src="${track.coverBase64}">`
    : `<div class="track-cover" style="...">🎵</div>`;
💅 Стилизация
Все стили находятся внутри тега <style> в файле index.html.

Основные CSS-классы
Класс	Назначение
.nav-bar	Верхняя навигационная панель
.nav-link	Ссылки навигации
.container	Основной контейнер с отступами
.page	Блок страницы (скрыт по умолчанию)
.music-grid, .playlist-grid	Сетка карточек (Grid layout)
.track-card, .playlist-card	Карточка трека или плейлиста
.track-cover	Обложка трека/плейлиста
.modal-overlay	Затемнённый фон модального окна
.modal-content	Контент модального окна
.mood-btn	Кнопки настроения
.flex-between	Утилита для Flexbox с space-between
Адаптивный дизайн
Медиа-запрос для мобильных устройств:

css
@media (max-width: 680px) {
    .container { padding: 1rem; }
    .nav-bar { gap: 0.6rem; }
    .nav-link { font-size: 0.9rem; padding: 0.3rem 0.8rem; }
}
🐛 Отладка и решение проблем
Проблема: треки не сохраняются после перезагрузки
Проверьте:

Открыта ли консоль браузера (F12) на наличие ошибок

Не превышен ли лимит localStorage (рекомендуемый размер файла до 8 МБ)

Вызывается ли saveTracks() после добавления трека

Проблема: изображения не отображаются
Проверьте:

Корректность Base64-строки в объекте трека

Правильность пути в атрибуте src (должен быть track.coverBase64)

Проблема: плейлисты не обновляются после удаления трека
Функция deleteTrackById() автоматически удаляет ID трека из всех плейлистов:

javascript
playlists.forEach(pl => { 
    pl.trackIds = pl.trackIds.filter(tid => tid !== id); 
});
🔒 Ограничения проекта
Проект не содержит:

backend-сервера

базы данных

облачной синхронизации

авторизации пользователей

реального API

Все данные работают только в браузере пользователя и привязаны к конкретному устройству и браузеру.

Технические ограничения
Ограничение	Значение	Причина
Максимальный размер файла	~8-10 МБ	Лимит localStorage
Количество треков	~20-50 (зависит от размера)	Производительность Base64
Формат аудио	MP3, OGG, WAV, M4A, AAC	Поддержка браузерами
Формат изображений	JPEG, PNG, WebP	Поддержка браузерами
💡 Идеи для развития
Backend
Можно подключить:

Node.js + Express

Python + Django/Flask

PHP

База данных
Подойдут:

PostgreSQL / MySQL (реляционные)

MongoDB (документо-ориентированная)

Улучшение хранения
Заменить localStorage на IndexedDB для:

хранения файлов любого размера

лучшей производительности

асинхронных операций

Дополнительный функционал
Возможные улучшения:

глобальный плеер с очередью воспроизведения

импорт/экспорт библиотеки (JSON)

поиск по трекам и плейлистам

облачная синхронизация (Google Drive / Dropbox)

PWA-режим для установки на устройство

визуализация аудио (спектрограмма)

горячие клавиши для управления плеером

тёмная/светлая тема (переключение)

режим «Поделиться плейлистом»

🛠️ Рекомендации по разработке
Инструменты
Рекомендуется использовать:

VS Code (редактор кода)

Live Server (расширение для локального сервера с автообновлением)

Chrome DevTools (отладка JavaScript, просмотр localStorage)

Полезные команды в консоли браузера
javascript
// Просмотр всех треков
console.log(tracks);

// Просмотр всех плейлистов
console.log(playlists);

// Очистка всех данных
localStorage.clear();

// Просмотр размера хранилища
console.log(localStorage.getItem('life_music_tracks_v2').length / 1024, 'KB');
