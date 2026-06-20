import React, { useState, useEffect, useRef } from 'react';
import { 
  Globe, Clock, User, Sparkles, Store, Box, 
  ShieldAlert, Swords, Film, Lock, Mail, ChevronRight, 
  Map, Music, Utensils, Building, Cpu, CheckCircle2, AlertTriangle, Loader2,
  Play, Pause, Image as ImageIcon, Volume2, Archive, Unlock, Compass, Zap, Eye, Video,
  Activity, Microscope, FlaskConical, BrainCircuit, MessageSquare, Send, ArrowLeft, RefreshCw
} from 'lucide-react';

// --- CSS Animations & Custom Styles ---
const customStyles = `
  @keyframes spin-slow { from { transform: rotate(0deg); } to { transform: rotate(360deg); } }
  @keyframes earth-spin { 0% { background-position: 0% 0; } 100% { background-position: 200% 0; } }
  @keyframes float { 0%, 100% { transform: translateY(0px); } 50% { transform: translateY(-20px); } }
  @keyframes pulse-glow { 0%, 100% { box-shadow: 0 0 40px rgba(59,130,246,0.4), inset 0 0 40px rgba(59,130,246,0.4); } 50% { box-shadow: 0 0 80px rgba(139,92,246,0.6), inset 0 0 80px rgba(139,92,246,0.6); } }
  @keyframes twinkle { 0%, 100% { opacity: 0.2; } 50% { opacity: 0.8; } }
  .animate-spin-slow { animation: spin-slow 20s linear infinite; }
  .animate-float { animation: float 6s ease-in-out infinite; }
  .animate-float-delayed { animation: float 6s ease-in-out 3s infinite; }
  .glow-sphere { animation: pulse-glow 4s ease-in-out infinite; }
  
  .earth-sphere {
    width: 100%; height: 100%; border-radius: 50%;
    background-image: url('https://upload.wikimedia.org/wikipedia/commons/thumb/c/cd/Land_ocean_ice_2048.jpg/1024px-Land_ocean_ice_2048.jpg');
    background-size: auto 100%;
    box-shadow: inset -20px -20px 40px rgba(0,0,0,0.8), inset 0px 0px 20px rgba(59,130,246,0.4), 0 0 30px rgba(59, 130, 246, 0.6);
    animation: earth-spin 20s linear infinite;
  }

  .glass-panel { background: rgba(17, 24, 39, 0.6); backdrop-filter: blur(12px); -webkit-backdrop-filter: blur(12px); border: 1px solid rgba(255, 255, 255, 0.1); }
  .glass-button { background: rgba(255, 255, 255, 0.05); backdrop-filter: blur(4px); border: 1px solid rgba(255, 215, 0, 0.3); transition: all 0.3s ease; }
  .glass-button:hover { background: rgba(255, 215, 0, 0.1); border-color: rgba(255, 215, 0, 0.8); box-shadow: 0 0 15px rgba(255, 215, 0, 0.3); }
  .text-gold { color: #FFD700; }
  
  .imax-screen { transform: perspective(1000px) rotateX(5deg); box-shadow: 0 20px 50px rgba(0, 255, 255, 0.2), inset 0 0 50px rgba(0,0,0,0.8); border-radius: 20px 20px 50px 50px; border-bottom: 5px solid rgba(0, 255, 255, 0.3); }

  input[type=range] { -webkit-appearance: none; width: 100%; background: transparent; }
  input[type=range]::-webkit-slider-thumb { -webkit-appearance: none; height: 24px; width: 24px; border-radius: 50%; background: #FFD700; cursor: pointer; margin-top: -10px; box-shadow: 0 0 10px #FFD700; }
  input[type=range]::-webkit-slider-runnable-track { width: 100%; height: 4px; cursor: pointer; background: rgba(255, 255, 255, 0.2); border-radius: 2px; }
  
  .scrollbar-hide::-webkit-scrollbar { display: none; }
  .scrollbar-hide { -ms-overflow-style: none; scrollbar-width: none; }

  /* Thêm custom scrollbar thanh mảnh cho Phá Án Y Khoa */
  .custom-scrollbar::-webkit-scrollbar { width: 6px; }
  .custom-scrollbar::-webkit-scrollbar-track { background: rgba(255,255,255,0.05); border-radius: 10px; }
  .custom-scrollbar::-webkit-scrollbar-thumb { background: rgba(0, 255, 255, 0.3); border-radius: 10px; }
  .custom-scrollbar::-webkit-scrollbar-thumb:hover { background: rgba(0, 255, 255, 0.6); }

  /* Font Times New Roman cho các mục Khám phá và Y khoa theo yêu cầu */
  .font-times { font-family: 'Times New Roman', Times, serif; }
`;

// --- Mock Data ---
const earthEras = [
  { index: 0, year: "-65M", era: "Kỷ Phấn Trắng", desc: "Thời kỳ rực rỡ và cũng là hồi kết của loài khủng long khổng lồ trước vụ va chạm thiên thạch.", map: "Pangea phân tách", clothes: "Không có", music: "Tiếng gầm rú của thiên nhiên hoang dã", food: "Động/thực vật nguyên thủy", architecture: "Rừng rậm nguyên sinh, núi lửa phun trào." },
  { index: 1, year: "-2000", era: "Thời kỳ Đồ Đồng", desc: "Sự trỗi dậy của các nền văn minh vĩ đại đầu tiên dọc theo các dòng sông lớn như sông Nile, Euphrates.", map: "Sông Nile & Lưỡng Hà", clothes: "Da thú, khố vải dệt", music: "Trống da thú, sáo xương", food: "Lúa mì, thịt nướng", architecture: "Kim tự tháp, hang động, lều cỏ." },
  { index: 2, year: "1000", era: "Đế Chế Cổ Đại", desc: "Giai đoạn hoàng kim của Đế quốc Đông La Mã và sự hình thành nhà nước Đại Cồ Việt tự chủ.", map: "Đế chế Đông La Mã / Đại Cồ Việt", clothes: "Khố / Áo chẽn lính", music: "Cồng chiêng, đàn đá", food: "Cơm nếp, bánh mì lúa mạch", architecture: "Thành quách bằng đá kiên cố." },
  { index: 3, year: "1225", era: "Hào khí Đông A", desc: "Triều đại nhà Trần oanh liệt, ba lần đại phá quân Nguyên Mông bảo vệ bờ cõi Đại Việt.", map: "Đại Việt", clothes: "Áo giao lĩnh, giáp phục da", music: "Nhã nhạc, kèn, trống đồng", food: "Gạo tẻ, cá kho, chè", architecture: "Kinh thành Thăng Long sầm uất." },
  { index: 4, year: "1600", era: "Phân Tranh & Giao Thương", desc: "Giai đoạn Trịnh - Nguyễn phân tranh, nhưng cũng là lúc thương cảng Hội An mở cửa giao thương nhộn nhịp với thế giới.", map: "Đàng Trong - Đàng Ngoài", clothes: "Áo tấc, khăn xếp", music: "Quan họ, nhã nhạc", food: "Món cung đình, sản vật địa phương", architecture: "Thương cảng Hội An kiến trúc Á Đông." },
  { index: 5, year: "1800", era: "Vương Triều Cuối Cùng", desc: "Nhà Nguyễn thống nhất đất nước, lấy quốc hiệu Đại Nam, xây dựng Kinh thành Huế tráng lệ.", map: "Đại Nam", clothes: "Áo ngũ thân", music: "Nhã nhạc cung đình Huế", food: "Bún bò, ẩm thực cung đình", architecture: "Kinh thành Huế lợp ngói lưu ly." },
  { index: 6, year: "1945", era: "Thế Chiến II & Độc Lập", desc: "Thế giới chìm trong khói lửa Thế chiến 2, đồng thời đánh dấu mốc lịch sử Việt Nam giành độc lập.", map: "Thế giới chia phe", clothes: "Quân phục kaki", music: "Nhạc cách mạng, hành khúc", food: "Lương khô, cơm độn khoai", architecture: "Lô cốt, thành phố đổ nát vì bom đạn." },
  { index: 7, year: "1990", era: "Thời kỳ Đổi Mới", desc: "Giai đoạn chuyển mình vĩ đại của kinh tế - xã hội, mở cửa hội nhập và thay đổi văn hóa mạnh mẽ.", map: "Việt Nam hội nhập", clothes: "Quần ống loe, sơ mi cắm thùng", music: "Nhạc Pop, Làn sóng xanh", food: "Phở bò, bánh mì, kem mút", architecture: "Nhà tập thể, phố phường nhiều xe đạp." },
  { index: 8, year: "2024", era: "Kỷ nguyên Số & AI", desc: "Trí tuệ nhân tạo, toàn cầu hóa và công nghệ thông tin chi phối mọi mặt của đời sống nhân loại.", map: "Thế giới phẳng", clothes: "Thời trang hiện đại, Streetwear", music: "Nhạc điện tử, Rap, Indie", food: "Ẩm thực Fusion, Trà sữa", architecture: "Tòa nhà chọc trời kính cường lực." },
  { index: 9, year: "2100", era: "Khai phá Thái Dương Hệ", desc: "Nhân loại bắt đầu lập thuộc địa trên Mặt Trăng và Sao Hỏa, đối mặt với biến đổi khí hậu khắc nghiệt.", map: "Trái Đất - Mặt Trăng", clothes: "Trang phục vi mạch điều nhiệt", music: "Synthwave không gian", food: "Sinh tố dinh dưỡng siêu nén", architecture: "Thành phố vòm kính chống bức xạ." },
  { index: 10, year: "2500", era: "Du Hành Không Gian", map: "Liên minh các hành tinh", desc: "Du hành liên sao trở thành hiện thực nhờ động cơ lỗ hổng không gian. Giao tiếp với các chủng tộc ngoài hành tinh.", clothes: "Đồ nano tự đổi nhiệt", music: "Sóng não kỹ thuật số", food: "Thực phẩm in 3D", architecture: "Trạm không gian khổng lồ hình vành khuyên." },
  { index: 11, year: "3000", era: "Kỷ nguyên Lượng tử", desc: "Con người tiến hóa thành thực thể phi vật chất, sống trong đa vũ trụ đồng bộ số hóa.", map: "Đa Vũ Trụ Đồng Bộ", clothes: "Hologram cá nhân", music: "Giao hưởng năng lượng lượng tử", food: "Sạc năng lượng trực tiếp vào tế bào", architecture: "Bẻ cong không gian, vật chất tự tái tạo." }
];

const roleplayScenarios = [
  {
    id: "scientist", role: "Nhà khoa học (Dự án Manhattan 1945)", 
    context: "Bạn đang làm việc trong phòng lab tại Los Alamos. Phản ứng phân hạch đang có dấu hiệu mất kiểm soát (Criticality accident).",
    choices: [
      { text: "Bỏ chạy ngay lập tức khỏi phòng thí nghiệm.", correct: false, feedback: "Bạn sống sót, nhưng phản ứng chuỗi tiếp tục diễn ra, gây rò rỉ phóng xạ diện rộng giết chết toàn bộ đội ngũ." },
      { text: "Dùng tay không tách hai khối Plutonium ra xa nhau.", correct: true, feedback: "Hành động anh hùng giống hệt Louis Slotin! Bạn ngăn chặn vụ nổ, nhưng bị nhiễm phóng xạ liều tử vong." },
      { text: "Đổ một xô nước lạnh vào lõi để làm mát.", correct: false, feedback: "Nước đóng vai trò là chất làm chậm nơ-tron, khiến phản ứng tăng tốc đột ngột và nổ tung." },
      { text: "Khởi động hệ thống hút chân không khẩn cấp.", correct: false, feedback: "Chân không không ảnh hưởng đến bức xạ hạt nhân. Căn phòng nhanh chóng bị ngập trong tia Gamma." }
    ]
  },
  {
    id: "doc1350", role: "Bác sĩ Thời Trung Cổ (Năm 1350)", 
    context: "Đại dịch Cái Chết Đen đang hoành hành tại Châu Âu. Một bệnh nhân đến với bạn bị sốt cao và nổi hạch.",
    choices: [
      { text: "Rạch tĩnh mạch để 'trích huyết' loại bỏ máu độc.", correct: false, feedback: "Làm bệnh nhân yếu đi, mất máu và nhiễm trùng nhanh hơn." },
      { text: "Đeo mặt nạ mỏ chim nhồi thảo mộc và cách ly họ.", correct: true, feedback: "Tuy mặt nạ vô dụng với vi khuẩn, nhưng 'cách ly' là hành động vô tình chính xác nhất thời bấy giờ." },
      { text: "Đắp hỗn hợp phân ếch và rễ cây lên hạch bạch huyết.", correct: false, feedback: "Gây nhiễm trùng huyết nghiêm trọng khiến bệnh nhân chết đau đớn." },
      { text: "Khuyên bệnh nhân đi cầu nguyện tập thể tại nhà thờ.", correct: false, feedback: "Tụ tập đông người là cách nhanh nhất để lây lan bọ chét mang mầm bệnh Y. pestis." }
    ]
  },
  {
    id: "roman", role: "Chiến binh La Mã (Năm 70 CN)", 
    context: "Bạn đang bảo vệ thành trì tại vùng biên giới Britannia. Một nhóm bộ tộc Celtic đang áp sát.",
    choices: [
      { text: "Cầm kiếm Gladius xông lên quyết chiến tay đôi.", correct: false, feedback: "Phá vỡ đội hình Testudo khiến bạn dễ dàng bị tiêu diệt bởi cung tên." },
      { text: "Lập đội hình Testudo (Con rùa) phòng thủ chờ lệnh.", correct: true, feedback: "Đội hình rùa giúp bảo vệ toàn bộ đơn vị khỏi tên đạn, chờ quân địch kiệt sức." },
      { text: "Ném giáo Pilum từ xa rồi lùi lại.", correct: true, feedback: "Giáo Pilum được thiết kế để uốn cong khi đâm trúng khiên địch, làm họ vướng víu." },
      { text: "Bỏ chạy về kinh thành Rome kêu gọi viện binh.", correct: false, feedback: "Tội đào ngũ trong quân đội La Mã bị trừng phạt bằng hình thức decimation (giết 1/10)." }
    ]
  },
  {
    id: "egypt", role: "Bác sĩ Ai Cập Cổ Đại (1500 TCN)", 
    context: "Một công nhân xây Kim Tự Tháp bị đau đầu dữ dội và co giật.",
    choices: [
      { text: "Khoan lỗ trên hộp sọ (Trepanation) để giải phóng ác quỷ.", correct: true, feedback: "Tuy ghê rợn, nhưng phẫu thuật sọ não sơ khai vô tình làm giảm áp lực nội sọ." },
      { text: "Cho uống chiết xuất vỏ cây liễu trắng để giảm đau.", correct: true, feedback: "Người Ai Cập cổ đại đã biết dùng vỏ cây liễu (chứa Salicylic acid - tiền chất Aspirin)." },
      { text: "Phủ bùn sông Nile lên đầu bệnh nhân.", correct: false, feedback: "Bùn sông chứa nhiều vi khuẩn, ký sinh trùng có thể làm bệnh trầm trọng hơn." },
      { text: "Đọc bùa chú ma thuật của thần Thoth.", correct: false, feedback: "Không có tác dụng thực tế nào về mặt y khoa." }
    ]
  },
  {
    id: "docNow", role: "Bác sĩ Hiện Tại (Năm 2024)", 
    context: "Phòng cấp cứu: Nam 60 tuổi ôm ngực trái, vã mồ hôi. Nghi ngờ Nhồi máu cơ tim cấp.",
    choices: [
      { text: "Lấy máu xét nghiệm men tim rồi chờ kết quả.", correct: false, feedback: "Chờ đợi kết quả máu làm mất 'Thời gian Vàng' cứu cơ tim." },
      { text: "Đo Điện tâm đồ (ECG) 12 chuyển đạo trong 10 phút đầu.", correct: true, feedback: "Đo ECG ngay lập tức giúp phát hiện STEMI để can thiệp mạch vành." },
      { text: "Cho bệnh nhân chạy máy chạy bộ gắng sức.", correct: false, feedback: "Gây tăng nhu cầu oxy cơ tim, có thể dẫn đến tử vong tại chỗ!" },
      { text: "Khám siêu âm bụng tổng quát.", correct: false, feedback: "Sai trọng tâm cấp cứu, làm lỡ mất thời gian tái tưới máu mạch vành." }
    ]
  },
  {
    id: "doc2200", role: "Bác sĩ Năm 2200", 
    context: "Bệnh nhân bị đứt tủy sống do tai nạn phi thuyền, gây liệt hai chi dưới.",
    choices: [
      { text: "Lắp đặt khung xương ngoại cốt (Exoskeleton).", correct: false, feedback: "Công nghệ cơ khí đã quá cũ ở thế kỷ 23." },
      { text: "Tiêm đàn Nanobot sinh học để tái tạo mạng lưới nơ-ron.", correct: true, feedback: "Nanobot sẽ đóng vai trò thợ xây, nối lại các nơ-ron trong vài giờ." },
      { text: "In 3D một cơ thể mới và cấy ghép não bộ.", correct: false, feedback: "Chi phí quá đắt đỏ và rủi ro không tương thích tâm lý." },
      { text: "Sử dụng gen sứa bất tử để kích hoạt hồi sinh.", correct: false, feedback: "Gen sứa chỉ giúp chống lão hóa cấp tế bào, không nối tủy sống nhanh được." }
    ]
  },
  {
    id: "archaeologist", role: "Nhà khảo cổ (Ai Cập 1922)", 
    context: "Bạn vừa tìm thấy lối vào một lăng mộ niêm phong nghìn năm tại Thung lũng các vị Vua.",
    choices: [
      { text: "Phá cửa xông vào ngay để tìm kho báu.", correct: false, feedback: "Không khí độc và nấm mốc yếm khí nghìn năm khiến bạn nhiễm bệnh hô hấp cấp." },
      { text: "Khoan một lỗ nhỏ, đưa nến vào kiểm tra không khí.", correct: true, feedback: "Giống hệt cách Howard Carter làm! Việc này giúp giải phóng khí độc và kiểm tra nồng độ oxy." },
      { text: "Gọi báo chí đến chụp ảnh trước khi mở.", correct: false, feedback: "Bị kẻ trộm mộ địa phương đánh hơi và cướp sạch trong đêm." },
      { text: "Dùng thuốc nổ Dynamite để mở rộng hầm.", correct: false, feedback: "Lăng mộ sụp đổ, chôn vùi bạn cùng toàn bộ di sản vô giá." }
    ]
  },
  {
    id: "astronaut", role: "Phi hành gia (Sao Hỏa 2050)", 
    context: "Bạn đang trên trạm nghiên cứu Sao Hỏa. Một cơn bão bụi khổng lồ cấp 8 đang càn quét đến.",
    choices: [
      { text: "Mặc đồ bảo hộ chạy ra ngoài neo chặt các tấm pin mặt trời.", correct: false, feedback: "Sức gió và đá vụn làm thủng bộ đồ, bạn mất áp suất và tử vong." },
      { text: "Chuyển trạm sang năng lượng nội trú và phong tỏa cửa.", correct: true, feedback: "Lựa chọn sinh tồn chuẩn xác. Tránh xa các lớp kính cường lực bị yếu đi do bão." },
      { text: "Khởi động tên lửa khẩn cấp bay lên quỹ đạo.", correct: false, feedback: "Tên lửa mất cân bằng do sức gió bão cực mạnh và phát nổ khi cất cánh." },
      { text: "Mở hệ thống lọc gió hút bụi vào để nghiên cứu.", correct: false, feedback: "Bụi mịn chứa Perchlorate độc hại sẽ phá hỏng màng lọc, giết chết thủy thủ đoàn." }
    ]
  }
];

const survivalItems = [
  { id: 'phone', name: 'Điện thoại', icon: '📱', desc: "Hết pin nhanh, không sóng." },
  { id: 'knife', name: 'Dao đa năng', icon: '🔪', desc: "Tự vệ, săn bắn." },
  { id: 'meds', name: 'Thuốc kháng sinh', icon: '💊', desc: "Chống nhiễm trùng." },
  { id: 'lighter', name: 'Bật lửa', icon: '🔥', desc: "Tạo lửa nhanh chóng." },
  { id: 'filter', name: 'Ống lọc nước', icon: '💧', desc: "Ngăn bệnh kiết lỵ, tiêu chảy." },
  { id: 'tent', name: 'Lều bạt', icon: '🏕️', desc: "Tránh mưa, côn trùng ban đêm." },
  { id: 'compass', name: 'La bàn', icon: '🧭', desc: "Định hướng trong rừng." },
  { id: 'rope', name: 'Cuộn dây thừng', icon: '🪢', desc: "Bẫy thú, leo trèo." },
  { id: 'gold', name: 'Thỏi vàng', icon: '💰', desc: "Vô dụng trong tự nhiên." }
];

const allMysteryItems = [
  { id: 1, name: "Nháp của Da Vinci", rarity: "Hiếm", desc: "Sổ tay phác thảo cỗ máy bay năm 1490.", icon: "📜" },
  { id: 2, name: "Mảnh thiên thạch", rarity: "Cực hiếm", desc: "Mảnh thiên thạch đã tuyệt diệt khủng long.", icon: "☄️" },
  { id: 3, name: "Đồng tiền La Mã", rarity: "Phổ thông", desc: "Đồng xu bạc Denarius thời Augustus.", icon: "🪙" },
  { id: 4, name: "Viên đá lượng tử", rarity: "Tương lai", desc: "Một thiết bị lưu trữ dữ liệu năm 2800.", icon: "💎" },
  { id: 5, name: "La bàn Jack Sparrow", rarity: "Huyền thoại", desc: "Chỉ về hướng thứ bạn muốn nhất.", icon: "🧭" },
  { id: 6, name: "Ống nghe Laennec", rarity: "Hiếm", desc: "Ống nghe y tế đầu tiên bằng gỗ (1816).", icon: "🩺" },
  { id: 7, name: "Lọ Penicillin gốc", rarity: "Cực hiếm", desc: "Kháng sinh đầu tiên thay đổi lịch sử y học.", icon: "🧫" },
];

const hanoiImg = "data:image/svg+xml;utf8,%3Csvg width='500' height='300' viewBox='0 0 500 300' xmlns='http://www.w3.org/2000/svg'%3E%3Cdefs%3E%3ClinearGradient id='bg' x1='0%25' y1='0%25' x2='0%25' y2='100%25'%3E%3Cstop offset='0%25' stop-color='%235C4033' /%3E%3Cstop offset='100%25' stop-color='%231a1412' /%3E%3C/linearGradient%3E%3C/defs%3E%3Crect width='500' height='300' fill='url(%23bg)'/%3E%3Ctext x='250' y='140' font-family='serif' font-size='64' font-weight='bold' fill='%23F3E5AB' text-anchor='middle' letter-spacing='4'%3EHÀ NỘI%3C/text%3E%3Ctext x='250' y='210' font-family='sans-serif' font-size='42' font-weight='bold' fill='%23D4AF37' text-anchor='middle' letter-spacing='10'%3E1980%3C/text%3E%3Cpath d='M 100 270 L 400 270 M 200 270 L 200 240 M 300 270 L 300 240' stroke='%238B7355' stroke-width='6' fill='none'/%3E%3C/svg%3E";

const memoryMarketHouses = [
  { id: 1, title: "Hà Nội 1980", desc: "Thời bao cấp, xếp hàng mua gạo bằng tem phiếu.", img: hanoiImg, promptContext: "Bạn là người dân sống ở Hà Nội thời kỳ Bao cấp năm 1980." },
  { id: 2, title: "Paris 1920", desc: "Kỷ nguyên vàng, nhạc Jazz và ánh sáng rực rỡ.", img: "https://images.unsplash.com/photo-1502602898657-3e91760cbb34?w=500&q=80", promptContext: "Bạn là một nghệ sĩ sống tại Paris hoa lệ vào những năm 1920 kỷ nguyên vàng." },
  { id: 3, title: "Tokyo 2150", desc: "Thành phố Neon ngập trong mưa axit và xe bay.", img: "https://images.unsplash.com/photo-1540959733332-eab4deabeeaf?w=500&q=80", promptContext: "Bạn là một cư dân công nghệ cao sống tại Tokyo Cyberpunk năm 2150." },
  { id: 4, title: "Ai Cập Cổ Đại", desc: "Người thợ xây cặm cụi kéo đá lên Kim Tự Tháp.", img: "https://images.unsplash.com/photo-1539667468225-eebb663053e6?w=500&q=80", promptContext: "Bạn là một người dân sống dưới triều đại pharaoh Ai Cập cổ đại." },
];

const battleQuestionBank = [
  { q: "Kháng sinh Penicillin được phát hiện bởi ai?", options: ["Albert Einstein", "Alexander Fleming", "Marie Curie", "Louis Pasteur"], ans: 1, explain: "Alexander Fleming phát hiện ra nấm Penicillium tiết ra chất diệt vi khuẩn vào năm 1928." },
  { q: "Đế quốc La Mã phương Tây sụp đổ vào năm nào?", options: ["476 CN", "1453 CN", "1000 TCN", "0"], ans: 0, explain: "Năm 476 CN, hoàng đế cuối cùng Romulus Augustulus bị lật đổ." },
  { q: "Người đầu tiên phát hiện ra vi khuẩn dưới kính hiển vi?", options: ["Antonie van Leeuwenhoek", "Robert Koch", "Galen", "Aristotle"], ans: 0, explain: "Ông được coi là 'Cha đẻ của Vi sinh vật học'." },
  { q: "Khủng long tuyệt chủng cách đây khoảng bao lâu?", options: ["10 triệu năm", "65 triệu năm", "100 triệu năm", "200 triệu năm"], ans: 1, explain: "Vụ va chạm thiên thạch Chicxulub cách đây 66 triệu năm đã kết thúc kỷ Phấn Trắng." },
  { q: "Thuyết tương đối hẹp (E=mc²) do ai công bố?", options: ["Isaac Newton", "Niels Bohr", "Albert Einstein", "Stephen Hawking"], ans: 2, explain: "Albert Einstein công bố phương trình nổi tiếng này vào năm 1905." },
  { q: "Người đầu tiên đặt chân lên mặt trăng là ai?", options: ["Yuri Gagarin", "Buzz Aldrin", "Michael Collins", "Neil Armstrong"], ans: 3, explain: "Neil Armstrong bước lên Mặt Trăng ngày 20/7/1969 trên tàu Apollo 11." },
  { q: "Cấu trúc phân tử DNA có hình dạng gì?", options: ["Chuỗi đơn", "Chuỗi xoắn kép", "Hình cầu", "Hình tam giác"], ans: 1, explain: "DNA có cấu trúc chuỗi xoắn kép (Double Helix) do Watson và Crick khám phá." },
  { q: "Hành tinh nào lớn nhất trong Hệ Mặt Trời?", options: ["Sao Hỏa", "Trái Đất", "Sao Thổ", "Sao Mộc"], ans: 3, explain: "Sao Mộc (Jupiter) có khối lượng gấp 2.5 lần tổng khối lượng tất cả hành tinh khác cộng lại." },
  { q: "Chiến tranh thế giới thứ hai kết thúc vào năm nào?", options: ["1918", "1939", "1945", "1954"], ans: 2, explain: "Chiến tranh kết thúc vào năm 1945 sau khi Nhật Bản đầu hàng." },
  { q: "Nền văn minh cổ đại nào xây dựng Machu Picchu?", options: ["Ai Cập", "La Mã", "Inca", "Maya"], ans: 2, explain: "Người Inca đã xây dựng khu tàn tích Machu Picchu ở Nam Mỹ." },
  { q: "Ai là người phát minh ra điện thoại đầu tiên?", options: ["Thomas Edison", "Nikola Tesla", "Alexander Graham Bell", "Guglielmo Marconi"], ans: 2, explain: "Alexander Graham Bell được cấp bằng sáng chế điện thoại đầu tiên năm 1876." },
  { q: "Quốc gia nào có diện tích lớn nhất thế giới?", options: ["Canada", "Trung Quốc", "Hoa Kỳ", "Nga"], ans: 3, explain: "Nga là quốc gia lớn nhất thế giới về diện tích." },
  { q: "Bộ phận nào trong tế bào chịu trách nhiệm sản xuất năng lượng?", options: ["Nhân tế bào", "Ti thể", "Màng tế bào", "Lưới nội chất"], ans: 1, explain: "Ti thể (Mitochondria) là nhà máy năng lượng của tế bào." },
  { q: "Đại dương nào lớn nhất trên Trái Đất?", options: ["Đại Tây Dương", "Thái Bình Dương", "Ấn Độ Dương", "Bắc Băng Dương"], ans: 1, explain: "Thái Bình Dương bao phủ 1/3 diện tích bề mặt Trái Đất." },
  { q: "Trong bảng tuần hoàn hóa học, nguyên tố O là ký hiệu của?", options: ["Vàng", "Sắt", "Oxy", "Bạc"], ans: 2, explain: "O là ký hiệu của nguyên tố Oxygen." },
  { q: "Triều đại nhà Trần ở Việt Nam đã mấy lần đánh thắng quân Nguyên Mông?", options: ["1 lần", "2 lần", "3 lần", "4 lần"], ans: 2, explain: "Quân dân nhà Trần đã 3 lần đại phá quân Nguyên Mông oanh liệt." }
];

const secretUniverseTopics = [
  {
    id: 'atlantis',
    title: 'Thành phố Atlantis',
    puzzle: { q: 'Đại dương nào được truyền thuyết cho là nơi nhấn chìm Atlantis?', a: ['đại tây dương', 'atlantic'] },
    questions: [
      { q: 'Thành phố Atlantis thực sự nằm ở đâu?', a: 'Theo triết gia Plato, Atlantis nằm ngoài "Cột chống trời của Hercules" (Eo biển Gibraltar). Tuy nhiên, cho đến nay đây vẫn chỉ là một huyền thoại chưa có bằng chứng khảo cổ xác thực, dù nhiều giả thuyết cho rằng nó có thể nằm ở Đại Tây Dương, Địa Trung Hải hay thậm chí là Nam Cực.' },
      { q: 'Vì sao Atlantis biến mất?', a: 'Truyền thuyết kể rằng nền văn minh này đã bị nhấn chìm xuống đáy biển chỉ trong một ngày đêm do sự trừng phạt của các vị thần vì sự suy đồi đạo đức và tham vọng thống trị của người dân.' }
    ]
  },
  {
    id: 'aliens',
    title: 'Người ngoài hành tinh',
    puzzle: { q: 'Khu vực tuyệt mật số bao nhiêu của Mỹ ở bang Nevada thường bị đồn đoán là nơi nghiên cứu UFO?', a: ['51', 'năm mươi mốt'] },
    questions: [
      { q: 'Chúng ta có đơn độc trong vũ trụ không?', a: 'Dựa trên phương trình Drake và sự rộng lớn của vũ trụ với hàng tỷ tỷ hành tinh, xác suất để Trái Đất là nơi duy nhất có sự sống là rất thấp. Tuy nhiên, theo nghịch lý Fermi, chúng ta vẫn chưa tìm thấy bằng chứng rõ ràng nào về họ.' },
      { q: 'Khu vực 51 có giấu người ngoài hành tinh không?', a: 'Khu vực 51 (Area 51) là một căn cứ quân sự tuyệt mật của Mỹ. Dù có nhiều thuyết âm mưu, chính phủ Mỹ khẳng định đây chỉ là nơi thử nghiệm các loại máy bay và hệ thống vũ khí mới của không quân.' }
    ]
  },
  {
    id: 'hypotheses',
    title: 'Các giả thuyết khoa học',
    puzzle: { q: 'Con mèo của nhà vật lý học nào nổi tiếng với trạng thái "vừa sống vừa chết" trong hộp kín?', a: ['schrödinger', 'schrodinger', 'schroedinger'] },
    questions: [
      { q: 'Thuyết Đa vũ trụ (Multiverse) là gì?', a: 'Là giả thuyết cho rằng vũ trụ của chúng ta chỉ là một trong vô số các vũ trụ tồn tại song song. Mỗi vũ trụ có thể có các định luật vật lý, hằng số và thậm chí là lịch sử tiến hóa hoàn toàn khác nhau.' },
      { q: 'Chúng ta đang sống trong một chương trình giả lập (Simulation Theory)?', a: 'Giả thuyết này cho rằng nếu các nền văn minh tiên tiến có thể tạo ra các mô phỏng cực kỳ chân thực về tổ tiên của họ, thì xác suất rất cao vũ trụ mà chúng ta đang sống thực chất chỉ là một mô phỏng máy tính.' }
    ]
  },
  {
    id: 'civilizations',
    title: 'Những nền văn minh bí ẩn',
    puzzle: { q: 'Nền văn minh cổ đại nào ở Trung Mỹ nổi tiếng với bộ lịch kết thúc chu kỳ vào năm 2012?', a: ['maya'] },
    questions: [
      { q: 'Điều gì đã xảy ra với nền văn minh Maya?', a: 'Nền văn minh Maya không biến mất hoàn toàn. Tuy nhiên, sự sụp đổ của các thành bang vĩ đại vào thế kỷ 9 được cho là do sự kết hợp của biến đổi khí hậu (hạn hán kéo dài), xung đột nội bộ và sự cạn kiệt tài nguyên.' },
      { q: 'Ai đã xây dựng nên bãi đá Stonehenge?', a: 'Stonehenge được xây dựng qua nhiều giai đoạn trong hàng ngàn năm bởi các nền văn hóa thời đồ đá và đồ đồng ở Anh. Mục đích chính xác vẫn là bí ẩn, có thể là một đài thiên văn, nơi hiến tế hoặc thánh địa chữa bệnh.' }
    ]
  }
];

// --- AI Integration Helpers ---
const callGeminiText = async (prompt, systemPrompt) => {
  const apiKey = ""; 
  const url = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-09-2025:generateContent?key=${apiKey}`;
  
  const attemptFetch = async (retries, delay) => {
      try {
          const res = await fetch(url, {
              method: 'POST',
              headers: { 'Content-Type': 'application/json' },
              body: JSON.stringify({
                  contents: [{ parts: [{ text: prompt }] }],
                  systemInstruction: { parts: [{ text: systemPrompt }] }
              })
          });
          if (!res.ok) throw new Error("HTTP error");
          const data = await res.json();
          return data.candidates?.[0]?.content?.parts?.[0]?.text || "";
      } catch(e) {
          if (retries > 0) {
              await new Promise(r => setTimeout(r, delay));
              return attemptFetch(retries - 1, delay * 2);
          }
          return null;
      }
  }
  return attemptFetch(3, 1000);
};

const safeParseJSON = (text) => {
  try {
    return JSON.parse(text.replace(/[`]{3}json/gi, '').replace(/[`]{3}/g, '').trim());
  } catch(e) { return null; }
};

const renderSafe = (data) => {
  if (data === null || data === undefined) return "";
  if (typeof data === 'object') {
    try {
      return Object.entries(data).map(([k, v]) => `${k}: ${typeof v === 'object' ? JSON.stringify(v) : v}`).join('\n');
    } catch (e) {
      return JSON.stringify(data);
    }
  }
  return String(data);
};

const FollowUpSearch = ({ contextText }) => {
  const [query, setQuery] = useState('');
  const [history, setHistory] = useState([]);
  const [loading, setLoading] = useState(false);

  const handleAsk = async () => {
    if(!query) return;
    setLoading(true);
    const newHistory = [...history, { role: 'user', text: query }];
    setHistory(newHistory);
    setQuery('');

    const sysPrompt = `Bạn là một người bản địa sống trong bối cảnh: "${contextText}". Hãy nhập vai và trả lời câu hỏi của khách du lịch thời gian. Trả lời ngắn gọn, sinh động bằng tiếng Việt.`;
    const res = await callGeminiText(query, sysPrompt);
    
    setHistory([...newHistory, { role: 'ai', text: res || "Mất tín hiệu kết nối với dòng thời gian..." }]);
    setLoading(false);
  };

  return (
    <div className="w-full flex flex-col h-full bg-black/40 rounded-2xl border border-gray-700 overflow-hidden">
      <div className="flex-1 overflow-y-auto p-4 space-y-4 scrollbar-hide">
        {history.length === 0 && <p className="text-gray-400 text-sm text-center italic mt-4">AI đã kết nối thành công. Hãy đặt câu hỏi về nơi này!</p>}
        {history.map((msg, i) => (
          <div key={i} className={`p-3 rounded-lg text-sm w-fit max-w-[85%] ${msg.role==='user' ? 'bg-cyan-900/50 text-white ml-auto border border-cyan-500/50 rounded-br-none' : 'bg-black/50 text-gray-300 border border-gray-600 rounded-bl-none'}`}>
            {msg.text}
          </div>
        ))}
        {loading && <div className="p-3 bg-black/50 w-fit rounded-lg border border-gray-600 rounded-bl-none"><Loader2 className="w-4 h-4 animate-spin text-cyan-500"/></div>}
      </div>

      <div className="p-3 bg-black/60 border-t border-gray-700 flex gap-2">
        <input 
          type="text" value={query} onChange={(e)=>setQuery(e.target.value)} onKeyDown={(e) => e.key === 'Enter' && handleAsk()}
          placeholder="Bạn muốn hỏi AI điều gì?" 
          className="flex-1 bg-black/50 border border-cyan-500/30 rounded-xl p-3 text-white focus:border-cyan-400 outline-none text-sm"
        />
        <button onClick={handleAsk} disabled={loading} className="bg-cyan-600 hover:bg-cyan-500 text-white px-4 rounded-xl transition-colors disabled:opacity-50"><Send size={18}/></button>
      </div>
    </div>
  );
}

// --- Main Application ---
export default function App() {
  const [currentView, setCurrentView] = useState('earth');
  const [isMenuOpen, setIsMenuOpen] = useState(false);
  const [collection, setCollection] = useState([]);
  const [hasOpenedBoxToday, setHasOpenedBoxToday] = useState(false);

  useEffect(() => {
    const savedCol = localStorage.getItem('chrono_collection');
    if (savedCol) setCollection(JSON.parse(savedCol));
    const lastOpened = localStorage.getItem('chrono_last_opened');
    if (lastOpened === new Date().toDateString()) setHasOpenedBoxToday(true);
  }, []);

  const Starfield = () => (
    <div className="fixed inset-0 z-0 overflow-hidden pointer-events-none bg-[#050510]">
      <div className="absolute inset-0 bg-gradient-to-br from-indigo-900/40 via-black to-blue-900/30"></div>
      {[...Array(50)].map((_, i) => (
        <div key={i} className="absolute bg-white rounded-full"
          style={{ top: `${Math.random() * 100}%`, left: `${Math.random() * 100}%`, width: `${Math.random() * 3}px`, height: `${Math.random() * 3}px`, animation: `twinkle ${Math.random() * 3 + 2}s infinite` }}
        />
      ))}
    </div>
  );

  // --- Core Views ---

  const EarthLiveView = () => {
    const [sliderIndex, setSliderIndex] = useState(8);
    const data = earthEras[sliderIndex];

    return (
      <div className="h-full flex flex-col items-center justify-between p-4 md:p-6 w-full max-w-7xl mx-auto z-10 relative overflow-y-auto scrollbar-hide py-10 font-times">
        <h2 className="text-3xl md:text-4xl font-black text-transparent bg-clip-text bg-gradient-to-r from-yellow-400 to-yellow-200 mt-4 drop-shadow-md text-center tracking-widest uppercase relative z-30 font-sans">Earth Live</h2>
        
        <div className="absolute inset-0 flex flex-col items-center justify-center pointer-events-none z-20 px-4">
            <h3 className="text-4xl md:text-6xl font-black text-gold mb-6 drop-shadow-[0_10px_30px_rgba(0,0,0,1)] uppercase tracking-wider text-center pointer-events-auto leading-tight font-sans">
              {data.era}
            </h3>
            <p className="text-lg md:text-2xl font-medium text-white max-w-4xl mx-auto drop-shadow-[0_5px_15px_rgba(0,0,0,1)] bg-black/40 px-8 py-5 rounded-3xl pointer-events-auto text-center border border-white/10 backdrop-blur-md leading-relaxed">
              "{data.desc}"
            </p>
        </div>

        <div className="relative w-56 h-56 md:w-[24rem] md:h-[24rem] my-auto animate-float flex-shrink-0 opacity-40 z-10 pointer-events-none">
          <div className="absolute inset-0 rounded-full border-4 border-blue-500/30 animate-spin-slow" style={{ borderStyle: 'dashed' }}></div>
          <div className="absolute inset-4 rounded-full border-2 border-purple-500/40 animate-spin-slow" style={{ animationDirection: 'reverse' }}></div>
          <div className="absolute inset-8 rounded-full overflow-hidden shadow-[0_0_80px_rgba(59,130,246,0.8)]">
            <div className="earth-sphere"></div>
            <div className="absolute top-0 left-0 w-full h-1 bg-cyan-400/50 shadow-[0_0_15px_#00FFFF] animate-[float_2s_linear_infinite]"></div>
          </div>
        </div>

        <div className="w-full max-w-5xl px-4 z-30 mt-auto bg-black/70 p-6 md:p-8 rounded-[2rem] border border-cyan-500/30 backdrop-blur-xl shadow-[0_0_40px_rgba(0,255,255,0.1)]">
          <div className="flex justify-between items-center mb-6">
             <span className="font-bold text-white text-2xl md:text-3xl bg-cyan-900/50 px-6 py-2 rounded-full border border-cyan-500/50 drop-shadow-md font-sans">Năm {data.year}</span>
          </div>
          <input type="range" min="0" max={earthEras.length - 1} value={sliderIndex} onChange={(e) => setSliderIndex(parseInt(e.target.value))} className="w-full h-3 bg-gray-700 rounded-lg appearance-none cursor-pointer" />
          <div className="flex justify-between text-xs md:text-sm text-cyan-400 mt-4 font-bold tracking-widest uppercase font-sans">
            <span>Quá Khứ</span><span>Hiện tại</span><span>Tương lai</span>
          </div>

          <div className="grid grid-cols-2 lg:grid-cols-4 gap-4 mt-6 text-left">
            <div className="flex flex-col gap-1 border-r border-white/10 pr-2"><span className="text-cyan-400 font-bold text-xs uppercase tracking-wider flex items-center gap-2 font-sans"><Map size={14}/> Địa lý</span><span className="text-white text-base md:text-lg">{data.map}</span></div>
            <div className="flex flex-col gap-1 border-r border-white/10 pr-2"><span className="text-cyan-400 font-bold text-xs uppercase tracking-wider flex items-center gap-2 font-sans"><Building size={14}/> Xã hội</span><span className="text-white text-base md:text-lg">{data.architecture}</span></div>
            <div className="flex flex-col gap-1 border-r border-white/10 pr-2"><span className="text-cyan-400 font-bold text-xs uppercase tracking-wider flex items-center gap-2 font-sans"><User size={14}/> Trang phục</span><span className="text-white text-base md:text-lg">{data.clothes}</span></div>
            <div className="flex flex-col gap-1"><span className="text-cyan-400 font-bold text-xs uppercase tracking-wider flex items-center gap-2 font-sans"><Utensils size={14}/> Ẩm thực</span><span className="text-white text-base md:text-lg">{data.food}</span></div>
          </div>
        </div>
      </div>
    );
  };

  const TimePortalView = () => {
    const [year, setYear] = useState('');
    const [location, setLocation] = useState('');
    const [result, setResult] = useState(null);
    const [loading, setLoading] = useState(false);

    const handleJump = async () => {
      if (!year) return;
      setLoading(true); setResult(null);
      const promptText = `Tôi bước khỏi cỗ máy thời gian năm ${year} tại ${location || 'Trái đất'}. Hãy mô tả chi tiết bằng tiếng Việt (4-5 câu) những gì MẮT THẤY, TAI NGHE trên da thịt để người dùng có cảm giác như đang đứng ở đó. Không cần tạo ảnh.`;
      const aiResponse = await callGeminiText(promptText, "Trả lời trực tiếp, giàu cảm xúc.");
      setResult(aiResponse || "Nhiễu loạn thời không.");
      setLoading(false);
    };

    return (
      <div className="h-full flex items-center justify-center p-4 md:p-6 z-10 relative overflow-y-auto scrollbar-hide py-20 font-times">
        <div className="glass-panel max-w-4xl w-full p-6 md:p-10 rounded-3xl my-auto">
          <div className="flex items-center gap-4 mb-8 border-b border-white/10 pb-6 font-sans">
            <Clock className="w-10 h-10 text-cyan-400 animate-spin-slow" />
            <div>
              <h2 className="text-2xl md:text-3xl font-black text-white">Time Portal</h2>
              <p className="text-cyan-200 mt-1 text-xs md:text-sm">Mô phỏng không gian quá khứ bằng ngôn ngữ AI (Đã tắt Media).</p>
            </div>
          </div>

          <div className="grid grid-cols-1 md:grid-cols-2 gap-6 mb-8 font-sans">
            <div><label className="block text-xs text-gray-400 mb-2 font-bold uppercase tracking-wider">Năm (VD: 1453, 2500)</label><input type="text" value={year} onChange={(e) => setYear(e.target.value)} className="w-full bg-black/60 border border-white/20 rounded-xl p-3 text-white text-base focus:border-cyan-400 outline-none" /></div>
            <div><label className="block text-xs text-gray-400 mb-2 font-bold uppercase tracking-wider">Địa điểm</label><input type="text" value={location} onChange={(e) => setLocation(e.target.value)} placeholder="Ai Cập, Sao Hỏa..." className="w-full bg-black/60 border border-white/20 rounded-xl p-3 text-white text-base focus:border-cyan-400 outline-none" /></div>
          </div>

          <button onClick={handleJump} disabled={loading || !year} className="w-full py-4 rounded-xl font-black text-lg text-black bg-gradient-to-r from-cyan-400 to-blue-500 disabled:opacity-50 hover:shadow-[0_0_30px_rgba(0,255,255,0.4)] transition-all flex justify-center gap-3 font-sans">
            {loading ? <Loader2 className="animate-spin w-5 h-5" /> : <Sparkles className="w-5 h-5" />} {loading ? 'Đang kết xuất Dữ Liệu...' : '✨ Khởi động'}
          </button>

          {result && (
            <div className="animate-in fade-in slide-in-from-bottom-4 duration-500 mt-8 space-y-6">
              <div className="bg-black/80 p-6 md:p-8 rounded-3xl border-l-4 border-cyan-500 text-left shadow-2xl">
                <p className="text-cyan-50 leading-relaxed text-lg md:text-xl tracking-wide drop-shadow-md">"{result}"</p>
              </div>
              <div className="p-5 glass-panel rounded-2xl border border-cyan-500/30 font-sans">
                 <h4 className="text-gold font-bold mb-2 uppercase tracking-widest text-xs">Ghi chú & Khám phá thêm</h4>
                 <FollowUpSearch contextText={result} />
              </div>
            </div>
          )}
        </div>
      </div>
    );
  };

  const DreamFutureView = () => {
    const [job, setJob] = useState(''); const [hobby, setHobby] = useState(''); const [country, setCountry] = useState('');
    const [result, setResult] = useState(null); const [loading, setLoading] = useState(false);

    const handlePredict = async () => {
      if (!job || !country) return;
      setLoading(true); setResult(null);
      const promptText = `Tôi làm ${job}, thích ${hobby} sống ở ${country}. Hãy viết một đoạn văn (khoảng 200 từ) dự đoán cuộc sống của tôi vào năm 2065 cực kỳ chân thực và mang màu sắc Sci-fi. (Không cần tạo ảnh, không video).`;
      const aiResponse = await callGeminiText(promptText, "Trả lời nhập vai như một AI dự báo tương lai.");
      setResult(aiResponse || "Lỗi nội suy.");
      setLoading(false);
    };

    return (
      <div className="h-full flex items-center justify-center p-4 md:p-6 z-10 relative overflow-y-auto py-20 scrollbar-hide font-times">
        <div className="glass-panel max-w-4xl w-full p-6 md:p-8 rounded-3xl my-auto">
          <div className="flex items-center gap-4 mb-8 border-b border-white/10 pb-4 font-sans"><Cpu className="w-10 h-10 text-purple-400 animate-pulse" /><div><h2 className="text-2xl md:text-3xl font-black text-white">Dream Future</h2><p className="text-purple-200 mt-1 text-xs md:text-sm">Dự đoán viễn cảnh bằng Văn bản AI (Đã tắt Media).</p></div></div>
          <div className="space-y-4 mb-6 font-sans">
            <input type="text" value={job} onChange={(e) => setJob(e.target.value)} placeholder="Nghề nghiệp (VD: Bác sĩ robot)" className="w-full bg-black/50 border border-purple-500/30 rounded-xl p-3 text-white text-base focus:border-purple-400 outline-none" />
            <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
              <input type="text" value={hobby} onChange={(e) => setHobby(e.target.value)} placeholder="Sở thích" className="w-full bg-black/50 border border-purple-500/30 rounded-xl p-3 text-white text-base focus:border-purple-400 outline-none" />
              <input type="text" value={country} onChange={(e) => setCountry(e.target.value)} placeholder="Quốc gia" className="w-full bg-black/50 border border-purple-500/30 rounded-xl p-3 text-white text-base focus:border-purple-400 outline-none" />
            </div>
          </div>
          <button onClick={handlePredict} disabled={loading || !job || !country} className="w-full py-4 mb-8 rounded-xl font-black text-lg text-white bg-gradient-to-r from-purple-600 to-indigo-600 disabled:opacity-50 flex justify-center items-center gap-3 font-sans">
            {loading ? <Loader2 className="animate-spin w-5 h-5" /> : <Sparkles className="w-5 h-5" />} Xem Viễn Cảnh Tương Lai
          </button>
          
          {result && (
            <div className="animate-in fade-in space-y-6">
              <div className="bg-black/80 p-6 md:p-8 rounded-3xl border-l-4 border-purple-500 text-left shadow-2xl">
                <p className="text-purple-50 text-lg md:text-xl font-medium leading-relaxed tracking-wide drop-shadow-md">"{result}"</p>
              </div>
              <div className="p-5 glass-panel rounded-2xl border border-purple-500/30 font-sans">
                 <h4 className="text-gold font-bold mb-2 uppercase tracking-widest text-xs">Trò chuyện với AI Tương Lai</h4>
                 <FollowUpSearch contextText={result} />
              </div>
            </div>
          )}
        </div>
      </div>
    );
  };

  const RoleplayView = () => {
    const [step, setStep] = useState(0);
    const [selectedRole, setSelectedRole] = useState(null);
    const [result, setResult] = useState(null);

    return (
      <div className="h-full flex items-center justify-center p-4 md:p-6 z-10 relative overflow-y-auto py-20 scrollbar-hide">
        <div className="glass-panel max-w-4xl w-full p-6 md:p-8 rounded-3xl text-center my-auto">
          <User className="w-16 h-16 mx-auto text-yellow-500 mb-4 animate-float" />
          <h2 className="text-3xl md:text-4xl font-black text-white mb-2">Become Someone</h2>
          <p className="text-cyan-200 mb-8 text-sm md:text-base">Nhập vai vào các nhân vật Lịch sử & Y khoa.</p>

          {step === 0 && (
            <div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-4 animate-in fade-in">
              {roleplayScenarios.map(scen => (
                <button key={scen.id} onClick={() => { setSelectedRole(scen); setStep(1); }} className="p-4 glass-button rounded-xl flex items-center justify-center gap-3 text-center hover:scale-105 transition-transform border border-yellow-500/30">
                  <div>
                    <h4 className="text-white font-bold text-sm md:text-base leading-tight">{scen.role}</h4>
                  </div>
                </button>
              ))}
            </div>
          )}

          {step === 1 && selectedRole && (
            <div className="space-y-6 animate-in fade-in">
              <h3 className="text-2xl md:text-3xl font-bold text-gold bg-black/30 px-6 py-3 rounded-xl inline-block uppercase tracking-widest mt-4">{selectedRole.role}</h3>
              <button onClick={() => setStep(2)} className="block mx-auto px-10 py-4 bg-gradient-to-r from-yellow-600 to-yellow-500 text-black rounded-full font-black text-lg hover:scale-105 transition-transform">Bắt đầu mô phỏng</button>
              <button onClick={() => setStep(0)} className="text-sm text-gray-500 underline mt-4">Trở lại kho nhân vật</button>
            </div>
          )}

          {step === 2 && selectedRole && (
            <div className="text-left animate-in zoom-in">
              <div className="bg-red-900/30 border-l-4 border-red-500 p-6 mb-6 flex gap-4 items-start rounded-r-xl">
                <AlertTriangle className="text-red-500 flex-shrink-0 mt-1 w-6 h-6 md:w-8 md:h-8" />
                <p className="text-white text-lg md:text-xl leading-relaxed">{selectedRole.context}</p>
              </div>
              <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
                {selectedRole.choices.map((choice, idx) => (
                  <button key={idx} onClick={() => { setResult(choice); setStep(3); }} className="w-full text-left p-4 md:p-6 rounded-2xl glass-button text-gray-200 hover:text-white hover:border-cyan-400 hover:bg-cyan-900/30 text-sm md:text-base transition-all shadow-lg">
                    {choice.text}
                  </button>
                ))}
              </div>
            </div>
          )}

          {step === 3 && result && (
            <div className="animate-in slide-in-from-bottom-4">
              <div className={`flex items-center justify-center gap-3 mb-6 text-2xl md:text-3xl font-black uppercase tracking-widest ${result.correct ? 'text-green-400 drop-shadow-[0_0_10px_rgba(74,222,128,0.5)]' : 'text-red-500 drop-shadow-[0_0_10px_rgba(239,68,68,0.5)]'}`}>
                {result.correct ? <CheckCircle2 size={32}/> : <AlertTriangle size={32}/>} {result.correct ? 'Chuẩn Xác!' : 'Sai Lầm!'}
              </div>
              <div className="bg-black/60 p-6 md:p-8 rounded-2xl border border-gray-600 text-left">
                <p className="text-gray-200 leading-relaxed text-sm md:text-base">{result.feedback}</p>
              </div>
              <button onClick={() => setStep(0)} className="mt-8 px-10 py-3 border-2 border-cyan-500 text-cyan-400 rounded-full hover:bg-cyan-500/20 font-bold uppercase">Kết thúc</button>
            </div>
          )}
        </div>
      </div>
    );
  };

  const SurvivalView = () => {
    const [selected, setSelected] = useState([]);
    const [survived, setSurvived] = useState(null);

    const toggleItem = (id) => {
      if (selected.includes(id)) setSelected(selected.filter(i => i !== id));
      else if (selected.length < 3) setSelected([...selected, id]);
    };

    const handleSurvive = () => {
      const s = selected;
      let score = 0; let analysis = "";
      if (s.includes('knife')) { score += 30; analysis += "✔️ Dao đa năng sinh tồn thiết yếu.\n"; } else analysis += "❌ Thiếu vũ khí sắc bén.\n";
      if (s.includes('lighter')) { score += 20; analysis += "✔️ Lửa giúp nấu ăn, sưởi ấm.\n"; } else analysis += "❌ Khó tạo lửa trong rừng ẩm.\n";
      if (s.includes('filter')) { score += 30; analysis += "✔️ Lọc nước giúp tránh dịch tả.\n"; } else analysis += "❌ Uống nước suối dễ ngộ độc.\n";
      if (s.includes('meds')) { score += 20; analysis += "✔️ Kháng sinh chống nhiễm trùng.\n"; }
      if (s.includes('gold') || s.includes('phone')) { score -= 20; analysis += "⚠️ Lãng phí chỗ chứa cho đồ công nghệ/vàng vô dụng.\n"; }

      setSurvived({ score: Math.max(0, score), success: score >= 60, analysis });
    };

    return (
      <div className="h-full flex items-center justify-center p-4 md:p-6 z-10 relative overflow-y-auto scrollbar-hide py-20">
        <div className="glass-panel max-w-4xl w-full p-6 md:p-8 rounded-3xl my-auto">
          <div className="flex items-center gap-4 mb-6 border-b border-white/10 pb-4"><ShieldAlert className="w-10 h-10 text-red-500" /><div><h2 className="text-2xl md:text-3xl font-bold text-white">Survival Challenge</h2><p className="text-gray-400 text-sm mt-1">Nhiệm vụ: Rừng rậm Amazon năm 1500.</p></div></div>
          {!survived ? (
            <>
              <p className="text-cyan-200 mb-4 font-bold text-center">Chọn đúng 3 vật dụng: <span className="bg-cyan-900/50 px-2 py-1 rounded-md">{selected.length}/3</span></p>
              <div className="grid grid-cols-2 md:grid-cols-3 gap-3 md:gap-4 mb-8">
                {survivalItems.map(item => (
                  <button key={item.id} onClick={() => toggleItem(item.id)} className={`p-4 rounded-xl flex flex-col items-center gap-2 border ${selected.includes(item.id) ? 'bg-cyan-900/40 border-cyan-400 shadow-[0_0_15px_rgba(0,255,255,0.2)]' : 'glass-button border-transparent'}`}>
                    <span className="text-3xl md:text-4xl">{item.icon}</span><span className="text-xs md:text-sm font-bold text-center">{item.name}</span><span className="text-[10px] text-gray-400 text-center leading-tight">{item.desc}</span>
                  </button>
                ))}
              </div>
              <button onClick={handleSurvive} disabled={selected.length !== 3} className="w-full py-4 rounded-xl font-black text-lg bg-gradient-to-r from-yellow-400 to-yellow-600 disabled:opacity-50 uppercase text-black">Bắt đầu sinh tồn</button>
            </>
          ) : (
            <div className="animate-in zoom-in text-center">
              <h3 className={`text-5xl font-black mb-2 ${survived.success ? 'text-green-400' : 'text-red-500'}`}>{survived.success ? 'SỐNG SÓT' : 'TỬ VONG'}</h3>
              <p className="text-white text-2xl mb-8">Điểm: {survived.score}/100</p>
              <div className="bg-black/50 p-6 rounded-xl border border-gray-700 text-left mb-8 space-y-2">
                {survived.analysis.split('\n').map((l, i) => l.trim() && <p key={i} className="text-gray-200 text-sm md:text-base">{l}</p>)}
              </div>
              <button onClick={() => { setSelected([]); setSurvived(null); }} className="px-10 py-3 border border-cyan-500 text-cyan-400 rounded-full font-bold uppercase hover:bg-cyan-900/30">Chơi lại</button>
            </div>
          )}
        </div>
      </div>
    );
  };

  const MysteryBoxView = () => {
    const [viewingCollection, setViewingCollection] = useState(false);
    const [justOpenedItem, setJustOpenedItem] = useState(null);

    const openBox = () => {
      if (hasOpenedBoxToday) return;
      const availableItems = allMysteryItems.filter(item => !collection.some(c => c.id === item.id));
      if (availableItems.length === 0) { alert("Bạn đã thu thập đủ!"); return; }
      const randomItem = availableItems[Math.floor(Math.random() * availableItems.length)];
      
      const newCollection = [...collection, randomItem];
      setJustOpenedItem(randomItem);
      setCollection(newCollection);
      setHasOpenedBoxToday(true);
      localStorage.setItem('chrono_collection', JSON.stringify(newCollection));
      localStorage.setItem('chrono_last_opened', new Date().toDateString());
    };

    if (viewingCollection) {
      return (
        <div className="h-full flex flex-col p-4 md:p-8 z-10 relative overflow-y-auto">
          <div className="flex flex-col sm:flex-row justify-between items-center mb-8 gap-4">
            <h2 className="text-3xl font-bold text-gold">Bộ Sưu Tập</h2>
            <button onClick={() => setViewingCollection(false)} className="px-6 py-2 border border-cyan-500 text-cyan-400 rounded-lg font-bold">Quay lại</button>
          </div>
          <div className="grid grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-4 md:gap-6">
            {collection.map(item => (
              <div key={item.id} className="glass-panel p-6 rounded-2xl text-center border border-yellow-500/30">
                <span className="text-5xl block mb-2">{item.icon}</span>
                <h4 className="font-bold text-white text-sm md:text-base mb-1">{item.name}</h4>
                <span className="text-[10px] bg-yellow-900 text-yellow-400 px-2 rounded-full">{item.rarity}</span>
              </div>
            ))}
          </div>
        </div>
      );
    }

    return (
      <div className="h-full flex items-center justify-center p-4 md:p-6 z-10 relative">
        <div className="text-center w-full max-w-lg">
          <div className="flex justify-between items-center mb-10">
            <h2 className="text-3xl font-bold text-gold">Hộp Bí Ẩn</h2>
            <button onClick={() => setViewingCollection(true)} className="flex items-center gap-2 text-sm text-cyan-400 bg-cyan-900/50 px-4 py-2 rounded-full border border-cyan-500/50">
              <Archive size={16}/> Xem túi ({collection.length})
            </button>
          </div>

          {!justOpenedItem ? (
            <div onClick={openBox} className={`relative w-48 h-48 md:w-64 md:h-64 mx-auto cursor-pointer group ${hasOpenedBoxToday ? 'opacity-50 grayscale' : 'hover:scale-110'}`}>
              {!hasOpenedBoxToday && <div className="absolute inset-0 bg-yellow-500/30 blur-3xl rounded-full"></div>}
              <Box className="w-full h-full text-yellow-400 relative z-10 drop-shadow-[0_0_20px_rgba(255,215,0,0.8)]" />
              <p className="mt-8 text-cyan-400 font-bold uppercase">{hasOpenedBoxToday ? 'Đã mở hôm nay' : 'Bấm để mở'}</p>
            </div>
          ) : (
            <div className="glass-panel p-8 rounded-3xl animate-in zoom-in border-2 border-yellow-500">
              <span className="text-8xl block mb-4">{justOpenedItem.icon}</span>
              <h3 className="text-2xl font-black text-white mb-2">{justOpenedItem.name}</h3>
              <p className="text-gray-300 text-sm mb-6">{justOpenedItem.desc}</p>
              <button onClick={() => setJustOpenedItem(null)} className="bg-yellow-500 text-black px-8 py-3 rounded-xl font-bold">Cất vào bộ sưu tập</button>
            </div>
          )}
        </div>
      </div>
    );
  };

  const MemoryMarketView = () => {
    const [openedHouse, setOpenedHouse] = useState(null);

    return (
      <div className="h-full p-6 md:p-8 z-10 relative overflow-y-auto scrollbar-hide py-10">
        <h2 className="text-3xl md:text-4xl font-bold text-white mb-2 flex items-center gap-3"><Store className="text-cyan-400" /> Memory Market</h2>
        <p className="text-cyan-200 mt-2">Đã tháo gỡ Media. Chat trực tiếp với AI đóng vai người bản địa để khám phá từng khu vực.</p>
        
        <div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-6 mt-8">
          {memoryMarketHouses.map(house => (
            <div key={house.id} onClick={() => setOpenedHouse(house)} className="glass-panel rounded-3xl overflow-hidden cursor-pointer group hover:-translate-y-3 hover:shadow-[0_15px_30px_rgba(0,255,255,0.2)] transition-all border border-gray-700 hover:border-cyan-500">
              <div className="h-48 relative"><img src={house.img} className="w-full h-full object-cover opacity-60 group-hover:opacity-100 group-hover:scale-110 transition-all duration-700" /><div className="absolute bottom-4 left-4 text-white font-bold text-xl">{house.title}</div></div>
              <div className="p-4 bg-black/40"><span className="text-sm font-bold text-cyan-400 flex items-center gap-2 uppercase tracking-widest"><Unlock size={16}/> Mở Cửa & Chat</span></div>
            </div>
          ))}
        </div>

        {openedHouse && (
          <div className="fixed inset-0 z-50 flex items-center justify-center p-4 bg-black/95 backdrop-blur-md animate-in fade-in duration-300">
            <div className="glass-panel max-w-5xl w-full h-[85vh] rounded-3xl overflow-hidden flex flex-col relative border border-cyan-500/50 shadow-[0_0_50px_rgba(0,255,255,0.2)]">
              <div className="p-6 bg-black/60 border-b border-gray-700 flex justify-between items-center">
                 <div>
                   <h3 className="text-3xl font-black text-gold drop-shadow-lg">{openedHouse.title}</h3>
                   <p className="text-gray-300 text-sm mt-1">{openedHouse.desc}</p>
                 </div>
                 <button onClick={() => setOpenedHouse(null)} className="px-6 py-3 bg-red-600/80 hover:bg-red-500 text-white rounded-xl font-bold flex items-center gap-2 transition-colors">
                    <ArrowLeft size={20}/> QUAY LẠI
                 </button>
              </div>
              <div className="flex-1 p-6 overflow-hidden bg-black/40">
                 <FollowUpSearch contextText={openedHouse.promptContext} />
              </div>
            </div>
          </div>
        )}
      </div>
    );
  };

  const TimeBattleView = () => {
    const [inGame, setInGame] = useState(false); 
    const [qIndex, setQIndex] = useState(0); 
    const [score, setScore] = useState(0);
    const [gameQuestions, setGameQuestions] = useState([]); 
    const [feedback, setFeedback] = useState(null);

    const handleStart = () => {
      const shuffled = [...battleQuestionBank].sort(() => 0.5 - Math.random());
      const selected10 = shuffled.slice(0, 10);
      setGameQuestions(selected10);
      setScore(0);
      setQIndex(0);
      setFeedback(null);
      setInGame(true);
    };

    const handleAnswer = (idx) => {
      if (feedback) return; 
      const currentQ = gameQuestions[qIndex];
      if (idx === currentQ.ans) { 
        setScore(score + 100); 
        setFeedback('correct'); 
      } else {
        setFeedback('wrong');
      }
      setTimeout(() => { 
        if (qIndex + 1 >= 10) {
           setInGame("END"); 
        } else { 
           setQIndex(qIndex + 1); 
           setFeedback(null);
        } 
      }, 3500); 
    };

    const currentQ = gameQuestions[qIndex];

    return (
      <div className="h-full flex items-center justify-center p-4 md:p-6 z-10 relative overflow-y-auto scrollbar-hide py-20">
        <div className="glass-panel max-w-3xl w-full p-6 md:p-10 rounded-3xl text-center border border-red-500/30">
          <Swords className="w-16 h-16 mx-auto text-red-500 mb-4 animate-bounce" />
          <h2 className="text-3xl md:text-4xl font-black text-white uppercase tracking-widest mb-8">Time Battle</h2>
          {!inGame ? (
             <div className="animate-in zoom-in">
               <p className="text-gray-300 text-lg mb-8">Hệ thống sẽ lấy ngẫu nhiên 10 câu hỏi lịch sử/khoa học từ DataBank. Không trùng lặp!</p>
               <button onClick={handleStart} className="bg-gradient-to-r from-red-700 to-red-500 px-10 py-4 rounded-full text-white font-black uppercase text-xl w-full md:w-auto shadow-[0_0_20px_rgba(239,68,68,0.5)] hover:scale-105 transition-transform">
                 Bắt đầu trận đấu (10 Câu)
               </button>
             </div>
          ) : inGame === "END" ? (
             <div className="animate-in zoom-in">
               <h3 className="text-5xl font-black text-gold mb-6">HOÀN THÀNH!</h3>
               <p className="text-3xl text-white mb-8">Tổng Điểm: <span className="text-cyan-400 font-black">{score} / 1000</span></p>
               <button onClick={handleStart} className="px-10 py-4 border-2 border-cyan-500 text-cyan-400 rounded-full font-bold uppercase hover:bg-cyan-900/40 transition-colors">Chơi lượt mới (Random lại)</button>
             </div>
          ) : currentQ ? (
            <div className="text-left animate-in fade-in">
              <div className="flex justify-between text-cyan-400 font-black mb-6 border-b border-white/10 pb-4 text-base md:text-lg uppercase">
                <span>Câu {qIndex + 1}/10</span>
                <span className="text-gold">Điểm: {score}</span>
              </div>
              <h3 className="text-2xl md:text-3xl font-bold text-white mb-8 leading-relaxed">{currentQ.q}</h3>
              <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
                {currentQ.options.map((opt, idx) => {
                  let btnColor = "border-gray-600 hover:border-cyan-400 hover:bg-cyan-900/30 text-gray-200 hover:text-white";
                  if (feedback) {
                    if (idx === currentQ.ans) btnColor = "bg-green-600/40 border-green-500 text-green-100 font-bold";
                    else btnColor = "opacity-30 border-red-500/50 text-gray-500";
                  }
                  return (
                    <button key={idx} onClick={() => handleAnswer(idx)} disabled={!!feedback} className={`p-5 rounded-2xl text-left border-2 transition-all text-lg ${btnColor}`}>
                      {opt}
                    </button>
                  );
                })}
              </div>
              {feedback && (
                <div className={`mt-8 p-5 rounded-xl border-l-4 animate-in slide-in-from-bottom-4 ${feedback === 'correct' ? 'bg-green-900/20 border-green-500' : 'bg-red-900/20 border-red-500'}`}>
                   <p className={`font-black text-xl mb-2 flex items-center gap-2 ${feedback === 'correct' ? 'text-green-400' : 'text-red-400'}`}>
                     {feedback === 'correct' ? <CheckCircle2/> : <AlertTriangle/>} {feedback === 'correct' ? 'CHÍNH XÁC' : 'SAI RỒI'}
                   </p>
                   <p className="text-gray-200 text-lg leading-relaxed">{currentQ.explain}</p>
                </div>
              )}
            </div>
          ) : null}
        </div>
      </div>
    );
  };

  const SecretUniverseView = () => {
    const [unlockedTopics, setUnlockedTopics] = useState([]);
    const [activePuzzle, setActivePuzzle] = useState(null);
    const [puzzleInput, setPuzzleInput] = useState('');
    const [puzzleError, setPuzzleError] = useState('');

    const handleUnlock = (topic) => {
      const answers = topic.puzzle.a;
      const normalizedInput = puzzleInput.trim().toLowerCase();
      if (answers.some(ans => normalizedInput.includes(ans))) {
        setUnlockedTopics([...unlockedTopics, topic.id]);
        setActivePuzzle(null);
        setPuzzleInput('');
        setPuzzleError('');
      } else {
        setPuzzleError('Đáp án chưa chính xác. Hãy suy nghĩ thêm nhé!');
      }
    };

    return (
      <div className="h-full flex flex-col p-6 md:p-10 z-10 relative overflow-y-auto scrollbar-hide py-20">
        <h2 className="text-4xl md:text-5xl font-black text-transparent bg-clip-text bg-gradient-to-r from-purple-400 to-cyan-500 mb-8 uppercase tracking-widest text-center">Secret Universe</h2>
        <div className="grid grid-cols-1 md:grid-cols-2 gap-6 max-w-6xl mx-auto w-full">
          {secretUniverseTopics.map(topic => {
            const isUnlocked = unlockedTopics.includes(topic.id);
            const isSolving = activePuzzle === topic.id;

            return (
              <div key={topic.id} className="glass-panel p-6 rounded-2xl border border-purple-500/30 hover:border-purple-400 transition-all duration-500 flex flex-col min-h-[300px]">
                <h3 className="text-2xl font-bold text-gold mb-6 text-center">{topic.title}</h3>
                
                {isUnlocked ? (
                  <div className="space-y-4 animate-in fade-in">
                    {topic.questions.map((qa, idx) => (
                      <div key={idx} className="bg-black/50 p-4 rounded-xl border-l-4 border-purple-500">
                        <p className="text-purple-300 font-bold mb-2">Hỏi: {qa.q}</p>
                        <p className="text-gray-200 text-sm leading-relaxed">Đáp: {qa.a}</p>
                      </div>
                    ))}
                  </div>
                ) : isSolving ? (
                  <div className="flex flex-col items-center justify-center flex-1 space-y-4 animate-in zoom-in w-full max-w-sm mx-auto">
                     <p className="text-gray-200 text-center font-bold text-lg leading-relaxed">{topic.puzzle.q}</p>
                     <input 
                       type="text" 
                       value={puzzleInput} 
                       onChange={(e) => setPuzzleInput(e.target.value)} 
                       onKeyDown={(e) => e.key === 'Enter' && handleUnlock(topic)}
                       placeholder="Nhập đáp án của bạn..." 
                       className="w-full bg-black/50 border border-purple-500/50 rounded-xl p-3 text-white text-center outline-none focus:border-purple-400"
                     />
                     {puzzleError && <p className="text-red-400 text-sm">{puzzleError}</p>}
                     <div className="flex gap-3 w-full mt-2">
                       <button onClick={() => { setActivePuzzle(null); setPuzzleError(''); setPuzzleInput(''); }} className="flex-1 py-3 rounded-xl bg-gray-800 text-white font-bold hover:bg-gray-700 transition-colors">Trở Lại</button>
                       <button onClick={() => handleUnlock(topic)} className="flex-1 py-3 rounded-xl bg-purple-600 text-white font-bold hover:bg-purple-500 shadow-[0_0_15px_rgba(168,85,247,0.4)] transition-colors">Giải Mã</button>
                     </div>
                  </div>
                ) : (
                  <div className="flex flex-col items-center justify-center flex-1 py-6 opacity-80 group cursor-pointer" onClick={() => setActivePuzzle(topic.id)}>
                    <Lock className="w-16 h-16 text-purple-500/50 group-hover:text-purple-400 group-hover:scale-110 transition-all mb-4" />
                    <p className="text-purple-300 font-bold uppercase tracking-widest text-sm text-center">Khu vực bị niêm phong</p>
                    <button className="mt-6 px-8 py-3 border-2 border-purple-500 text-purple-400 rounded-full font-bold group-hover:bg-purple-900/30 transition-colors uppercase tracking-wider text-sm">Giải Mã Để Mở</button>
                  </div>
                )}
              </div>
            );
          })}
        </div>
      </div>
    );
  };

  const TimeCapsuleView = () => {
    const [message, setMessage] = useState("");
    const [selectedYear, setSelectedYear] = useState(1);
    const [loading, setLoading] = useState(false);
    const [reply, setReply] = useState(null);

    const handleSend = async () => {
      if (!message) return;
      setLoading(true); setReply(null);
      const prompt = `Đây là tin nhắn mà bản thân tôi trong quá khứ đã viết: "${message}"`;
      const sysPrompt = `Bạn là phiên bản TƯƠNG LAI của người dùng ở mốc ${selectedYear} năm sau. Nhiệm vụ của bạn là nhận tin nhắn này và gửi lại một "tín hiệu phản hồi đa chiều". Hãy phản hồi bằng tiếng Việt đầy cảm xúc sâu sắc và lời khuyên. Bắt đầu bằng: "Tín hiệu nhận được. Chào tôi của ${selectedYear} năm trước..."`;

      const responseText = await callGeminiText(prompt, sysPrompt);
      setReply(responseText || "Lỗi giao thức thời gian. Không thể kết nối với tương lai.");
      setLoading(false);
    };

    return (
      <div className="h-full flex items-center justify-center p-6 z-10 relative overflow-y-auto py-20 scrollbar-hide">
        <div className="glass-panel max-w-3xl w-full p-10 rounded-3xl border border-indigo-500/20 shadow-[0_0_30px_rgba(79,70,229,0.1)] my-auto">
          <div className="flex items-center gap-5 mb-10 border-b border-white/10 pb-6"><Mail className="w-12 h-12 text-indigo-400 animate-pulse" /><div><h2 className="text-4xl font-black text-white uppercase tracking-widest">Time Capsule</h2><p className="text-indigo-200 mt-1 text-lg">Gửi thông điệp đến tương lai.</p></div></div>
          {!reply ? (
            <div className="animate-in fade-in">
              <div className="space-y-6 mb-8">
                <textarea value={message} onChange={(e) => setMessage(e.target.value)} placeholder="Viết ước mơ..." className="w-full h-48 bg-black/60 border border-indigo-500/40 rounded-2xl p-6 text-white text-lg focus:border-indigo-400 outline-none resize-none"></textarea>
                <div className="pt-4"><label className="block text-sm font-bold uppercase tracking-widest text-gray-400 mb-4">Gửi Đến Bản Thân Ở Mốc:</label><div className="flex gap-4">
                    {[1, 5, 10].map(yr => ( <button key={yr} onClick={() => setSelectedYear(yr)} className={`flex-1 py-4 border rounded-xl text-white font-bold transition-all text-lg ${selectedYear === yr ? 'bg-indigo-600 border-indigo-400 shadow-[0_0_15px_rgba(79,70,229,0.5)]' : 'border-indigo-500/50 hover:bg-indigo-600/30'}`}>{yr} Năm Tới</button> ))}
                </div></div>
              </div>
              <button onClick={handleSend} disabled={loading || !message} className="w-full bg-gradient-to-r from-indigo-700 to-indigo-500 text-white font-black text-xl py-5 rounded-2xl flex justify-center items-center gap-3 transition-all disabled:opacity-50">
                {loading ? <Loader2 className="animate-spin w-6 h-6" /> : <Sparkles className="w-6 h-6" />} {loading ? "Đang kết nối..." : "✨ Gửi & Lắng Nghe Phản Hồi"}
              </button>
            </div>
          ) : (
            <div className="animate-in slide-in-from-bottom-8 duration-700">
              <div className="bg-indigo-950/40 border border-indigo-400/50 rounded-2xl p-8 shadow-[0_0_40px_rgba(79,70,229,0.3)]">
                 <div className="flex items-center gap-3 mb-6 text-indigo-300 font-mono text-sm border-b border-indigo-500/30 pb-3"><div className="w-3 h-3 bg-red-500 rounded-full animate-pulse"></div><span>GIAO THỨC BẢO MẬT: NHẬN TÍN HIỆU TỪ NĂM +{selectedYear}</span></div>
                 <p className="text-indigo-100 text-lg leading-relaxed whitespace-pre-line italic font-serif">{reply}</p>
                 <button onClick={() => { setReply(null); setMessage(""); }} className="mt-8 px-8 py-3 bg-indigo-900/50 text-indigo-300 border border-indigo-500/50 rounded-full font-bold hover:bg-indigo-800 uppercase text-sm tracking-widest">Đóng kênh liên lạc</button>
              </div>
            </div>
          )}
        </div>
      </div>
    );
  };

  const MemoryAIView = () => {
    const [strengths, setStrengths] = useState('');
    const [weaknesses, setWeaknesses] = useState('');
    const [loading, setLoading] = useState(false);
    const [cards, setCards] = useState([]); 
    const [cardIndex, setCardIndex] = useState(0);
    const [errorMsg, setErrorMsg] = useState(null);
    const [isFlipped, setIsFlipped] = useState(false);

    const generateFlashcards = async () => {
      if(!strengths || !weaknesses) {
         setErrorMsg("Vui lòng nhập cả điểm mạnh và điểm yếu."); return;
      }
      setLoading(true); setErrorMsg(null); setCards([]); setCardIndex(0); setIsFlipped(false);
      
      const prompt = `Học sinh có điểm mạnh: "${strengths}", điểm yếu: "${weaknesses}". 
Hãy đánh giá xem thông tin có hợp lý (có nghĩa/có liên quan đến học tập hoặc y khoa/khoa học không). 
Nếu KHÔNG hợp lý (nhập linh tinh), trả về JSON: {"error": "Lý do vì sao vô lý..."}. 
Nếu HỢP LÝ, hãy tạo MỘT MẢNG 10 FLASHCARD TẬP TRUNG VÀO ĐIỂM YẾU để củng cố kiến thức. 
Trả về JSON đúng chuẩn: {"cards": [{"q": "câu hỏi ngắn", "a": "đáp án giải thích ngắn gọn"}]}`;
      
      const resText = await callGeminiText(prompt, "Chỉ trả về định dạng JSON hợp lệ.");
      setLoading(false);
      
      const parsed = safeParseJSON(resText);
      if(!parsed) { setErrorMsg("AI phản hồi lỗi định dạng. Vui lòng thử lại."); return; }
      if(parsed.error) { setErrorMsg(parsed.error); return; }
      if(parsed.cards && Array.isArray(parsed.cards) && parsed.cards.length > 0) {
         setCards(parsed.cards);
      } else {
         setErrorMsg("Không tạo được flashcard. Vui lòng thử lại.");
      }
    };

    return (
      <div className="h-full flex items-center justify-center p-4 md:p-6 z-10 relative overflow-y-auto scrollbar-hide py-20">
        <div className="glass-panel max-w-4xl w-full p-6 md:p-10 rounded-3xl border border-orange-500/30 text-center my-auto">
          <BrainCircuit className="w-16 h-16 text-orange-400 mx-auto mb-4" />
          <h2 className="text-3xl md:text-4xl font-black text-white mb-2 uppercase tracking-widest">Memory AI</h2>
          <p className="text-orange-200 mb-8">Trợ lý AI cá nhân hóa. Tự động tạo 10 Flashcard khắc phục điểm yếu của bạn.</p>

          {cards.length === 0 ? (
             <div className="animate-in fade-in space-y-6 max-w-2xl mx-auto">
                <div className="text-left">
                  <label className="block text-green-400 font-bold mb-2 uppercase">Điểm Mạnh của bạn:</label>
                  <textarea value={strengths} onChange={e=>setStrengths(e.target.value)} placeholder="Ví dụ: Giỏi chẩn đoán hình ảnh X-Quang, tư duy logic tốt..." className="w-full bg-black/60 border border-green-500/50 rounded-xl p-4 text-white focus:border-green-400 outline-none h-24 resize-none"></textarea>
                </div>
                <div className="text-left">
                  <label className="block text-red-400 font-bold mb-2 uppercase">Điểm Yếu của bạn:</label>
                  <textarea value={weaknesses} onChange={e=>setWeaknesses(e.target.value)} placeholder="Ví dụ: Hay quên giải phẫu hệ thần kinh, nhầm lẫn tác dụng phụ của thuốc..." className="w-full bg-black/60 border border-red-500/50 rounded-xl p-4 text-white focus:border-red-400 outline-none h-24 resize-none"></textarea>
                </div>
                
                {errorMsg && <p className="text-red-400 font-bold bg-red-900/30 p-3 rounded-lg border border-red-500/50">{errorMsg}</p>}
                
                <button onClick={generateFlashcards} disabled={loading} className="w-full bg-orange-600 hover:bg-orange-500 py-4 rounded-xl text-white font-black text-xl shadow-[0_0_20px_rgba(249,115,22,0.4)] disabled:opacity-50 transition-all">
                  {loading ? <Loader2 className="w-6 h-6 animate-spin mx-auto"/> : "PHÂN TÍCH & TẠO 10 FLASHCARD"}
                </button>
             </div>
          ) : (
             <div className="animate-in zoom-in duration-500">
               <div className="flex justify-between items-center mb-6 text-sm font-bold text-gray-400 border-b border-white/10 pb-4">
                 <button onClick={()=>setCards([])} className="hover:text-white flex items-center gap-2"><RefreshCw size={16}/> Tạo Bộ Mới</button>
                 <span className="text-orange-400 text-lg">Thẻ {cardIndex + 1} / {cards.length}</span>
               </div>
               
               <div className="relative perspective-1000 w-full h-[300px] cursor-pointer" onClick={() => setIsFlipped(!isFlipped)}>
                 <div className={`w-full h-full absolute transition-all duration-500 transform-style-3d ${isFlipped ? 'rotate-y-180' : ''}`}>
                    <div className="absolute w-full h-full backface-hidden bg-black/80 border-2 border-orange-500 rounded-3xl p-8 flex flex-col items-center justify-center shadow-[0_0_30px_rgba(249,115,22,0.2)] hover:border-orange-400">
                       <span className="text-orange-400 font-black uppercase tracking-widest mb-4">MẶT TRƯỚC (CÂU HỎI)</span>
                       <p className="text-2xl md:text-3xl text-white font-bold">{renderSafe(cards[cardIndex].q)}</p>
                       <p className="text-gray-500 text-sm absolute bottom-4 animate-pulse">Chạm để lật thẻ</p>
                    </div>
                    <div className="absolute w-full h-full backface-hidden rotate-y-180 bg-orange-900/40 border-2 border-green-500 rounded-3xl p-8 flex flex-col items-center justify-center">
                       <span className="text-green-400 font-black uppercase tracking-widest mb-4">MẶT SAU (ĐÁP ÁN)</span>
                       <p className="text-xl md:text-2xl text-white leading-relaxed">{renderSafe(cards[cardIndex].a)}</p>
                    </div>
                 </div>
               </div>
               
               <div className="flex justify-center gap-4 mt-8">
                 <button onClick={()=>{setCardIndex(Math.max(0, cardIndex-1)); setIsFlipped(false);}} disabled={cardIndex===0} className="px-8 py-3 bg-gray-800 hover:bg-gray-700 text-white rounded-full font-bold disabled:opacity-30">Trước</button>
                 <button onClick={()=>{setCardIndex(Math.min(cards.length-1, cardIndex+1)); setIsFlipped(false);}} disabled={cardIndex===cards.length-1} className="px-8 py-3 bg-orange-600 hover:bg-orange-500 text-white rounded-full font-bold disabled:opacity-30 shadow-[0_0_15px_rgba(249,115,22,0.5)]">Tiếp Theo</button>
               </div>
             </div>
          )}
        </div>
      </div>
    );
  };

  const AIPatientView = () => {
    const [messages, setMessages] = useState([]);
    const [input, setInput] = useState('');
    const [loading, setLoading] = useState(false);

    const initChat = () => { setMessages([{ role: 'ai', text: "Bác sĩ ơi, tôi mệt quá... Hôm nay tôi đến khám vì thấy trong người bất thường từ sáng hôm qua." }]); };
    useEffect(() => { initChat(); }, []);

    const handleSend = async () => {
      if(!input) return;
      const newMsgs = [...messages, { role: 'user', text: input }];
      setMessages(newMsgs);
      setInput(''); setLoading(true);

      const sysPrompt = `Bạn là bệnh nhân viêm ruột thừa cấp giấu bệnh. Chỉ khai triệu chứng mệt mỏi, đau bụng buồn nôn khi hỏi. Nếu bác sĩ chỉ định siêu âm, máu, trả kết quả mô phỏng. Không xưng bệnh. Trả lời cực ngắn.`;
      const prompt = `Lịch sử: ${newMsgs.map(m=>m.role+": "+m.text).join('\n')}\nBác sĩ: ${input}\nBệnh nhân:`;
      const res = await callGeminiText(prompt, sysPrompt);
      setMessages([...newMsgs, { role: 'ai', text: res || "Đau quá..." }]);
      setLoading(false);
    };

    return (
      <div className="h-full flex flex-col p-4 md:p-8 z-10 relative">
        <div className="glass-panel max-w-4xl w-full mx-auto flex flex-col h-full rounded-3xl overflow-hidden border-2 border-emerald-500/30">
          <div className="p-6 border-b border-white/10 bg-emerald-900/20 flex items-center gap-4"><Activity className="w-10 h-10 text-emerald-400 animate-pulse" /><div><h2 className="text-2xl font-black text-white">AI Patient</h2><p className="text-emerald-200 text-sm">Hỏi bệnh & Xét nghiệm.</p></div><button onClick={initChat} className="ml-auto text-sm bg-black/50 px-4 py-2 rounded-lg border border-gray-600">Reset</button></div>
          <div className="flex-1 overflow-y-auto p-6 space-y-6 scrollbar-hide">
            {messages.map((m, i) => (<div key={i} className={`flex ${m.role === 'user' ? 'justify-end' : 'justify-start'}`}><div className={`max-w-[85%] p-4 rounded-2xl text-lg ${m.role==='user' ? 'bg-emerald-600 text-white rounded-br-none' : 'bg-black/60 border border-gray-600 text-gray-200 rounded-bl-none'}`}>{m.text}</div></div>))}
            {loading && <div className="flex justify-start"><div className="bg-black/60 p-4 rounded-2xl border border-gray-600 rounded-bl-none"><Loader2 className="w-6 h-6 animate-spin text-emerald-500" /></div></div>}
          </div>
          <div className="p-4 bg-black/50 border-t border-white/10 flex gap-2"><input type="text" value={input} onChange={(e)=>setInput(e.target.value)} onKeyDown={(e)=>e.key==='Enter'&&handleSend()} placeholder="Hỏi bệnh nhân..." className="flex-1 bg-black/50 border border-emerald-500/50 rounded-xl p-4 text-white outline-none focus:border-emerald-400" /><button onClick={handleSend} disabled={loading} className="bg-emerald-600 px-6 rounded-xl text-white font-bold"><Send/></button></div>
        </div>
      </div>
    );
  };

  const MysteryDiagnosisView = () => {
    const [caseData, setCaseData] = useState(null); const [diagnosis, setDiagnosis] = useState(''); const [feedback, setFeedback] = useState(null); const [loading, setLoading] = useState(false);
    const getCase = async () => { setLoading(true); setFeedback(null); setDiagnosis(''); const res = await callGeminiText("Tạo ca lâm sàng. JSON: {symptoms, vitals, labs, hidden_disease}", "Chỉ trả JSON"); try{ setCaseData(safeParseJSON(res)); }catch(e){ setCaseData({symptoms:"Đau ngực", vitals:"Mạch 100", labs:"ECG ST chênh", hidden_disease:"STEMI"}); } setLoading(false); };
    useEffect(() => { getCase(); }, []);
    const handleSubmit = async () => { if(!diagnosis) return; setLoading(true); setFeedback(await callGeminiText(`Bệnh: ${caseData.hidden_disease}. SV chẩn đoán: ${diagnosis}. Đánh giá 3 câu.`, "Giáo sư Y khoa")); setLoading(false); };
    return (
      <div className="h-full flex items-center justify-center p-4 z-10 relative overflow-y-auto scrollbar-hide py-20 font-times">
        <div className="glass-panel max-w-5xl w-full p-8 rounded-3xl border border-blue-500/30 mx-auto">
          <div className="flex justify-between items-center mb-6 border-b border-white/10 pb-4 font-sans"><div className="flex items-center gap-3"><Microscope className="w-10 h-10 text-blue-400"/><h2 className="text-2xl md:text-3xl font-black text-white">Phá Án Y Khoa</h2></div><button onClick={getCase} className="bg-blue-900/50 text-blue-300 px-6 py-2 rounded-lg hover:bg-blue-800 transition-colors font-bold uppercase tracking-wider text-sm border border-blue-500/50">Ca mới</button></div>
          {!caseData ? (<div className="py-20 flex justify-center"><Loader2 className="w-16 h-16 text-blue-500 animate-spin" /></div>) : (
            <div className="grid grid-cols-1 md:grid-cols-12 gap-8 font-sans items-start">
              <div className="md:col-span-7 space-y-4 bg-black/40 p-6 rounded-2xl border border-gray-700 font-sans max-h-[55vh] overflow-y-auto pr-4 custom-scrollbar shadow-inner">
                <div><h4 className="text-blue-400 font-bold uppercase tracking-widest text-xs mb-2">Triệu chứng</h4><p className="text-gray-200 text-sm md:text-base whitespace-pre-wrap break-words leading-relaxed">{renderSafe(caseData?.symptoms)}</p></div>
                <div><h4 className="text-blue-400 font-bold uppercase tracking-widest text-xs mb-2">Sinh hiệu</h4><p className="text-gray-200 text-sm md:text-base whitespace-pre-wrap break-words leading-relaxed">{renderSafe(caseData?.vitals)}</p></div>
                <div><h4 className="text-blue-400 font-bold uppercase tracking-widest text-xs mb-2">Cận lâm sàng (Lab/Hình ảnh)</h4><p className="text-gray-200 text-sm md:text-base whitespace-pre-wrap break-words leading-relaxed">{renderSafe(caseData?.labs)}</p></div>
              </div>
              <div className="md:col-span-5 flex flex-col space-y-4 font-sans h-full">
                <textarea value={diagnosis} onChange={e=>setDiagnosis(e.target.value)} disabled={!!feedback} className="min-h-[160px] flex-1 resize-y bg-black/60 border border-blue-500/50 rounded-xl p-4 text-white text-base leading-relaxed shadow-[inset_0_0_15px_rgba(0,0,0,0.5)] focus:border-blue-400 outline-none" placeholder="Nhập chẩn đoán chi tiết..."></textarea>
                {!feedback && <button onClick={handleSubmit} className="bg-blue-600 hover:bg-blue-500 py-4 rounded-xl font-black text-white uppercase tracking-widest transition-colors shadow-[0_0_20px_rgba(37,99,235,0.4)] text-sm md:text-base flex justify-center items-center gap-2">Nộp Chẩn Đoán</button>}
                {feedback && <div className="bg-blue-900/20 p-6 rounded-xl border border-blue-500 max-h-[40vh] overflow-y-auto custom-scrollbar"><p className="text-blue-50 whitespace-pre-wrap leading-relaxed text-sm md:text-base">{feedback}</p></div>}
              </div>
            </div>
          )}
        </div>
      </div>
    );
  };

  const FutureMedicineView = () => {
    const topics = ["Robot Phẫu Thuật", "Chỉnh Sửa Gen", "Nanorobot", "Bệnh Viện 2200"]; const [selected, setSelected] = useState(topics[0]); const [result, setResult] = useState(null); const [loading, setLoading] = useState(false);
    const generateFuture = async () => { setLoading(true); setResult(null); const res = await callGeminiText(`Dự đoán chi tiết y khoa về ${selected} năm 2200. Viết 200 từ thật sci-fi.`, "AI Dự báo"); setResult(res); setLoading(false); };
    return (
      <div className="h-full flex items-center justify-center p-4 z-10 relative overflow-y-auto scrollbar-hide py-20">
        <div className="glass-panel max-w-4xl w-full p-8 rounded-3xl border border-purple-500/30">
          <div className="flex items-center gap-4 mb-8 border-b border-white/10 pb-4"><FlaskConical className="w-12 h-12 text-purple-400" /><div><h2 className="text-3xl font-black text-white uppercase">Lab Tương Lai</h2></div></div>
          <div className="flex flex-wrap gap-3 mb-6">{topics.map(t => (<button key={t} onClick={()=>setSelected(t)} className={`px-4 py-2 rounded-full font-bold border ${selected === t ? 'bg-purple-600' : 'text-purple-300'}`}>{t}</button>))}</div>
          <button onClick={generateFuture} disabled={loading} className="w-full bg-purple-600 py-4 rounded-xl font-bold text-white">{loading ? <Loader2 className="animate-spin mx-auto"/> : "Mô Phỏng"}</button>
          {result && <div className="bg-black/60 p-6 mt-6 rounded-xl border-l-4 border-purple-500 text-purple-100 text-lg italic leading-relaxed">"{result}"</div>}
        </div>
      </div>
    );
  };

  // --- Menu Setup ---
  const navItems = [
    // Nhóm: Khám Phá
    { id: 'earth', name: 'Earth Live', icon: Globe, group: 'Khám Phá' },
    { id: 'portal', name: 'Time Portal', icon: Clock, group: 'Khám Phá' },
    { id: 'future', name: 'Dream Future', icon: Cpu, group: 'Khám Phá' },
    { id: 'capsule', name: 'Time Capsule', icon: Mail, group: 'Khám Phá' },
    
    // Nhóm: Trải Nghiệm
    { id: 'roleplay', name: 'Become Someone', icon: User, group: 'Trải Nghiệm' },
    { id: 'survival', name: 'Survival Challenge', icon: ShieldAlert, group: 'Trải Nghiệm' },
    
    // Nhóm: Giải Trí
    { id: 'market', name: 'Memory Market', icon: Store, group: 'Giải Trí' },
    { id: 'box', name: 'Mystery Box', icon: Box, group: 'Giải Trí' },
    { id: 'battle', name: 'Time Battle', icon: Swords, group: 'Giải Trí' },
    { id: 'secret', name: 'Secret Universe', icon: Compass, group: 'Giải Trí' },
    
    // Nhóm: Y Khoa
    { id: 'aipatient', name: 'AI Patient', icon: Activity, group: 'Y Khoa' },
    { id: 'mysterydiag', name: 'Phá Án Y Khoa', icon: Microscope, group: 'Y Khoa' },
    { id: 'futuremed', name: 'Lab Tương Lai', icon: FlaskConical, group: 'Y Khoa' },
    { id: 'memoryai', name: 'Memory AI', icon: BrainCircuit, group: 'Y Khoa' },
  ];

  const renderContent = () => {
    switch (currentView) {
      case 'earth': return <EarthLiveView />;
      case 'portal': return <TimePortalView />;
      case 'future': return <DreamFutureView />;
      case 'capsule': return <TimeCapsuleView />;
      case 'roleplay': return <RoleplayView />;
      case 'survival': return <SurvivalView />;
      case 'market': return <MemoryMarketView />;
      case 'box': return <MysteryBoxView />;
      case 'battle': return <TimeBattleView />;
      case 'secret': return <SecretUniverseView />;
      case 'aipatient': return <AIPatientView />;
      case 'mysterydiag': return <MysteryDiagnosisView />;
      case 'futuremed': return <FutureMedicineView />;
      case 'memoryai': return <MemoryAIView />;
      default: return <EarthLiveView />;
    }
  };

  return (
    <>
      <style>{customStyles}</style>
      <style>{`
        /* Thêm style để lật thẻ 3D */
        .perspective-1000 { perspective: 1000px; }
        .transform-style-3d { transform-style: preserve-3d; }
        .backface-hidden { backface-visibility: hidden; }
        .rotate-y-180 { transform: rotateY(180deg); }
      `}</style>
      
      <div className="min-h-screen bg-black text-white font-sans overflow-hidden flex selection:bg-cyan-500/30">
        
        {/* Tắt tự động phát nhạc trên code block này, có nút góc dưới phải để bật */}
        <audio id="global-bgm" loop src="https://upload.wikimedia.org/wikipedia/commons/1/17/Space_ambient_music.ogg"></audio>

        <Starfield />

        {/* Backdrop for mobile menu */}
        {isMenuOpen && <div className="fixed inset-0 bg-black/60 z-40 md:hidden backdrop-blur-sm" onClick={() => setIsMenuOpen(false)}></div>}

        {/* Sidebar Navigation */}
        <div className={`fixed md:relative z-50 h-full glass-panel border-r border-white/10 transition-all duration-300 flex flex-col shadow-2xl md:shadow-none
          ${isMenuOpen ? 'w-72 translate-x-0' : '-translate-x-full md:translate-x-0 w-0 md:w-20 lg:w-64'} 
        `}>
          <div className="p-4 flex items-center justify-between border-b border-white/5 h-16">
            <div className={`flex items-center gap-3 overflow-hidden whitespace-nowrap ${(!isMenuOpen && 'md:w-0 lg:w-auto')}`}>
              <Globe className="text-cyan-400 animate-spin-slow min-w-[24px]" />
              <span className={`font-black text-xl tracking-wider text-transparent bg-clip-text bg-gradient-to-r from-cyan-400 to-blue-600 transition-opacity ${(!isMenuOpen && 'md:opacity-0 lg:opacity-100')}`}>
                CHRONO
              </span>
            </div>
            <button onClick={() => setIsMenuOpen(!isMenuOpen)} className="hidden lg:block p-1 rounded hover:bg-white/10 text-gray-400">
              <ChevronRight className={`transition-transform ${isMenuOpen ? 'rotate-180' : ''}`} />
            </button>
          </div>

          <div className="flex-1 overflow-y-auto py-2 scrollbar-hide">
            <ul className="space-y-1 px-3">
              {navItems.map((item, index) => {
                const showGroupLabel = index === 0 || navItems[index-1].group !== item.group;
                return (
                  <React.Fragment key={item.id}>
                    {showGroupLabel && (isMenuOpen || window.innerWidth >= 1024) && (
                       <div className={`text-[10px] text-gray-500 font-bold uppercase tracking-widest mt-6 mb-2 pl-2 ${(!isMenuOpen && 'md:hidden lg:block')}`}>{item.group}</div>
                    )}
                    <li>
                      <button
                        onClick={() => { setCurrentView(item.id); if(window.innerWidth < 768) setIsMenuOpen(false); }}
                        className={`w-full flex items-center gap-4 px-3 py-3 rounded-xl transition-all group overflow-hidden ${
                          currentView === item.id ? 'bg-cyan-900/40 text-cyan-300 border border-cyan-500/30 shadow-[inset_0_0_10px_rgba(0,255,255,0.1)]' : 'text-gray-400 hover:bg-white/5 hover:text-white'
                        }`}
                        title={item.name}
                      >
                        <item.icon size={22} className={`min-w-[22px] ${currentView === item.id ? 'text-cyan-400' : 'group-hover:text-cyan-200'}`} />
                        <span className={`text-sm font-bold whitespace-nowrap transition-opacity ${(!isMenuOpen && 'md:opacity-0 lg:opacity-100')}`}>{item.name}</span>
                      </button>
                    </li>
                  </React.Fragment>
                );
              })}
            </ul>
          </div>
        </div>

        {/* Mobile menu toggle Button */}
        <button className="md:hidden fixed top-4 left-4 z-50 p-2.5 glass-panel rounded-xl border border-cyan-500/30 text-cyan-400" onClick={() => setIsMenuOpen(true)}>
           <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round"><line x1="3" y1="12" x2="21" y2="12"></line><line x1="3" y1="6" x2="21" y2="6"></line><line x1="3" y1="18" x2="21" y2="18"></line></svg>
        </button>

        <button className="fixed bottom-6 right-6 z-50 p-4 glass-panel rounded-full hover:bg-cyan-900/50 border border-cyan-500/50 shadow-[0_0_20px_rgba(0,255,255,0.3)] group"
          onClick={() => { const a = document.getElementById('global-bgm'); a.paused ? a.play() : a.pause(); }}
        >
          <Music className="w-5 h-5 text-cyan-400 group-hover:animate-pulse" />
        </button>

        {/* Main Content Area */}
        <div className="flex-1 relative h-screen w-full bg-[#050510]">
          {renderContent()}
        </div>
      </div>
    </>
  );
}
