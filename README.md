推し診断

<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<title>推し分析診断（試作版）</title>
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<style>
body {
    font-family: sans-serif;
    padding: 20px;
    max-width: 800px;
    margin: auto;
}
h1 {
    text-align: center;
}
form {
    background: #f5f5f5;
    padding: 15px;
    border-radius: 10px;
}
label {
    font-weight: bold;
}
select, input {
    width: 100%;
    padding: 6px;
    margin: 5px 0 15px;
}
button {
    padding: 10px 15px;
    margin-right: 10px;
    cursor: pointer;
}
table {
    width: 100%;
    margin-top: 20px;
    border-collapse: collapse;
}
td, th {
    border: 1px solid #ccc;
    padding: 5px;
    text-align: center;
}
#resultText {
    margin-top: 20px;
    font-weight: bold;
}
</style>
</head>
<body>

<h1>推し分析診断（試作版）</h1>

<form id="oshiForm">
    <label>名前：</label>
    <input type="text" id="name">

    <label>性別：</label>
    <select id="gender">
        <option>男</option><option>女</option><option>その他</option>
    </select>

    <label>肌の色：</label>
    <select id="skin">
        <option>赤</option><option>オレンジ</option><option>薄橙色(肌色)</option>
        <option>黄色</option><option>緑色</option><option>ミント色</option>
        <option>青色</option><option>紫色</option><option>ピンク色</option>
        <option>茶色(褐色)</option><option>白色</option><option>黒色</option>
        <option>灰色</option>
    </select>

    <label>目のメイン色：</label>
    <select id="eye">
        <option>赤</option><option>オレンジ</option><option>薄橙色(肌色)</option>
        <option>黄色</option><option>緑色</option><option>ミント色</option>
        <option>青色</option><option>紫色</option><option>ピンク色</option>
        <option>茶色(褐色)</option><option>クリーム色</option><option>白色</option>
        <option>黒色</option><option>灰色</option><option>虹色</option>
    </select>

    <label>髪のメイン色：</label>
    <select id="hair">
        <option>赤</option><option>オレンジ</option><option>黄色</option>
        <option>緑色</option><option>ミント色</option><option>青色</option>
        <option>紫色</option><option>ピンク色</option><option>茶色</option>
        <option>クリーム色</option><option>白色</option><option>黒色</option>
        <option>灰色</option><option>虹色</option>
    </select>

    <label>イメージカラー：</label>
    <select id="color">
        <option>赤</option><option>オレンジ</option><option>黄色</option>
        <option>緑色</option><option>ミント色</option><option>青色</option>
        <option>紫色</option><option>ピンク色</option><option>茶色</option>
        <option>クリーム色</option><option>白色</option><option>黒色</option>
        <option>灰色</option>
    </select>

    <label>兄弟構成：</label>
    <select id="family">
        <option>1番上</option><option>中間子</option><option>末っ子</option>
        <option>1人っ子</option><option>不明</option>
    </select>

    <button type="button" onclick="addOshi()">追加</button>
</form>

<h2>入力データ一覧</h2>
<table id="dataTable">
    <tr><th>名前</th><th>性別</th><th>肌</th><th>目</th><th>髪</th><th>カラー</th><th>兄弟構成</th></tr>
</table>

<button onclick="analyze()">分析する</button>

<canvas id="chart" width="400" height="400"></canvas>

<div id="resultText"></div>

<script>
let oshiList = [];
let chart = null;

// 入力データ追加
function addOshi() {
    const data = {
        name: document.getElementById("name").value,
        gender: document.getElementById("gender").value,
        skin: document.getElementById("skin").value,
        eye: document.getElementById("eye").value,
        hair: document.getElementById("hair").value,
        color: document.getElementById("color").value,
        family: document.getElementById("family").value,
    };
    oshiList.push(data);

    const row = document.createElement("tr");
    Object.values(data).forEach(v=>{
        const td = document.createElement("td");
        td.textContent = v;
        row.appendChild(td);
    });
    document.getElementById("dataTable").appendChild(row);

    document.getElementById("name").value = "";
}

// 項目ごとの集計
function countCategory(key) {
    let result = {};
    oshiList.forEach(o=>{
        const v = o[key];
        result[v] = (result[v] || 0) + 1;
    });
    return result;
}

function analyze() {
    if (oshiList.length === 0) return;

    const category = "hair"; // ← 今は「髪の色」を分析対象に（必要に応じて変更可）
    const counts = countCategory(category);

    const labels = Object.keys(counts);
    const data = Object.values(counts);

    if (chart) chart.destroy();
    const ctx = document.getElementById("chart");
    chart = new Chart(ctx, {
        type: "pie",
        data: {
            labels: labels,
            datasets: [{ data: data }]
        }
    });

    // 傾向文章
    let max = data[0];
    let maxLabel = labels[0];
    for (let i=1;i<data.length;i++){
        if (data[i] > max){
            max = data[i];
            maxLabel = labels[i];
        }
    }

    document.getElementById("resultText").textContent =
        `あなたの推しは「${maxLabel}」のキャラが多いようです！（${oshiList.length}人中${max}人）`;
}

</script>

</body>
</html>
