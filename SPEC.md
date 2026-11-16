# SPEC — Luyện nói tiếng Trung (camera + tài liệu)

## PM decisions (override spec ambiguity)
- Deliverable: ONE self-contained `index.html` (Tailwind CDN, pdf.js CDN, PapaParse CDN, @mediapipe/selfie_segmentation CDN). No build step, no Vite/React.
- All visible UI text in Vietnamese.
- Must work over `http://localhost` (camera needs secure context). Test with `python3 -m http.server`.
- Graceful degradation: if camera denied/unavailable, show Vietnamese message in camera pane, rest of app still works. If MediaPipe fails to load or FPS < ~12, auto-fallback to raw video + Vietnamese toast.
- Pin CDN versions (pdf.js: set GlobalWorkerOptions.workerSrc to matching CDN worker).

## Original spec
1. Theme: system sans-serif only. Bg #FFFFFF + dot grid `radial-gradient(#CBD5E1 1.25px, transparent 1.25px)` 20px. Ink #3B1E4A. Accent #E8642C. Secondary surfaces #F8FAFC/#F3F0F5 with 1.5px #3B1E4A borders. "汉字" seal badge.
2. Layout: 100dvh, overflow hidden. Default Top/Bottom split: camera 40% / content 60%, draggable resizer (#3B1E4A bar, #E8642C grip pill), range 15–85%, mouse+touch. Floating PiP mode: content 100%, camera = draggable 130px circle with #E8642C border + shadow. Also Left/Right split for wide screens.
3. Camera: front/rear switch (mobile), webcam select (desktop). "Lật gương" mirror toggle, default on for front cam. Selfie segmentation cycle: Tắt → Làm mờ nền → Nền trơn (off-white/dot-grid). Smooth FPS, fallback on lag.
4. Content viewer (modal / compact input: URL, file picker, drag-drop, paste):
   - YouTube (watch?v=, youtu.be/, shorts/) → youtube-nocookie.com/embed/ID?playsinline=1.
   - Images jpg/png/webp: pinch zoom, wheel zoom, pan.
   - PDF: all pages via pdf.js, devicePixelRatio-crisp, vertical scroll.
   - Anki CSV/TSV/TXT: PapaParse auto-delimiter (comma/semicolon/tab), UTF-8, strip HTML tags (<br>, <b>...) and [sound:...]. Card View (Chinese col 28–40px #3B1E4A, pinyin + meaning below) / Table View toggle ("Dạng thẻ lớn" / "Dạng bảng").
   - "Dán chữ trực tiếp" tab: render pasted text, leading-relaxed, floating A-/A+ (20–56px).
5. Overlay canvas on content, 3 modes: "Chạm nội dung" (pointer-events none), "Chỉ tay Laser" (glowing #E8642C dot + trail fading ~1.2s), "Bút Highlight" (rgba(232,100,44,0.35) persistent strokes) + "Xóa nét". Canvas must resize with pane and handle DPR.
6. Collapsible floating toolbar bottom-right. Collapsed: pill "Công cụ" (#3B1E4A bg, #E8642C icon) + quick toggle Chạm nội dung ↔ Chỉ Laser. Expanded sections: Nội dung (YouTube / Tải file / Dán văn bản), Cỡ chữ (A-/A+ when CSV/Text), Bố cục (Trên-Dưới / Bong bóng tròn PiP / Trái-Phải), Camera (Đổi Cam Trước-Sau / Lật gương / Xóa phông), Bút chỉ (Chạm / Laser / Highlight / Xóa nét), Ghi hình (Beta): getDisplayMedia + mic via MediaRecorder → download file; if unsupported (iOS Safari) show Vietnamese tooltip suggesting built-in phone screen recording.
7. `touch-action: none` on resizer, PiP circle, drawing canvas. No pull-to-refresh/back-swipe on these.
