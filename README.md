<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Corrugated Box Calculator</title>

<style>
body {
    font-family: Arial;
    margin: 0;
    background: linear-gradient(135deg,#74ebd5,#acb6e5);
}

.container {
    display: flex;
    gap: 10px;
    padding: 10px;
    justify-content: center;
}

.frame {
    width: 8in;
    height: 6in;
    background: #fff;
    border-radius: 10px;
    padding: 10px;
}

.rate-box {
    width: 6in;
    height: 6in;
    background: #fff;
    border-radius: 10px;
    padding: 8px;
    display: flex;
    flex-direction: column;
}

.btn-row {
    display: flex;
    gap: 5px;
}

.btn-row button {
    flex: 1;
}

.rate-scroll {
    flex: 1;
    overflow-y: auto;
}

table {
    width: 100%;
    border-collapse: collapse;
    font-size: 12px;
}

th, td {
    border: 1px solid #ccc;
    padding: 5px;
    text-align: center;
}

th {
    background: #2c3e50;
    color: white;
    position: sticky;
    top: 0;
}

.calc-box {
    background: #f4f4f4;
    padding: 8px;
    border-radius: 8px;
}

input, select {
    width: 60px;
}

button {
    padding: 5px;
    margin: 3px;
    border: none;
    border-radius: 5px;
    color: white;
    cursor: pointer;
}

.calc {background:green;}
.clear {background:orange;}
.export {background:blue;}

.result {
    text-align:center;
    font-weight:bold;
    color:green;
}

.table-container {
    height: calc(100% - 180px);
    overflow-y: auto;
}
</style>
</head>

<body>

<div class="container">

<!-- LEFT -->
<div class="frame">

<h3 align="center">📦 BOX CALCULATOR</h3>

<div class="calc-box">
L:<input type="number" id="l">
W:<input type="number" id="w">
H:<input type="number" id="h">

<br><br>

PLY:
<select id="ply" onchange="setRateFromPly()">
<option value="">Select Ply</option>
</select>

Rate:<input type="number" id="rate">
Print:<input type="number" id="print">

<select id="type">
<option value="normal">Normal</option>
<option value="cf">C/F</option>
</select>

<br>
<button class="calc" onclick="calculate()">Calculate</button>
<button class="clear" onclick="clearData()">Clear</button>
<button class="export" onclick="exportExcel()">Export</button>

<div class="result" id="result">₹ 0</div>
</div>

<div class="table-container">
<table id="resultTable">
<tr>
<th>#</th>
<th>Type</th>
<th>Size (L×W×H)</th>
<th>Rate</th>
<th>Print</th>
<th>Total</th>
<th>X</th>
</tr>
</table>
</div>

</div>

<!-- RIGHT -->
<div class="rate-box">

<h4 align="center">RATE LIST</h4>

<div class="btn-row">
<button onclick="enableEdit()" style="background:#e67e22;">Edit</button>
<button onclick="saveRates()" style="background:green;">Save</button>
</div>

<div class="rate-scroll">
<table>
<tr>
<th>S.No</th>
<th>Particular</th>
<th>OM</th>
<th>LAXSHY</th>
<th>BVS</th>
<th>Pooja</th>
</tr>

<tr><td>1</td><td>3 PLY</td><td>29</td><td>31</td><td></td><td></td></tr>
<tr><td>2</td><td>3 PLY OUTOMATIC</td><td>38</td><td></td><td>33</td><td></td></tr>
<tr><td>3</td><td>5PLY V-2</td><td>38</td><td>39</td><td>39</td><td>39</td></tr>
<tr><td>4</td><td>5PLY 200 LBS</td><td>52</td><td></td><td></td><td>45</td></tr>
<tr><td>5</td><td>5PLY 240 OUTOMATIC</td><td>49</td><td>52</td><td>48</td><td>48</td></tr>
<tr><td>6</td><td>5PLY 275 OUTOMATIC</td><td></td><td></td><td>57</td><td></td></tr>
<tr><td>7</td><td>5PLY 300 OUTOMATIC</td><td></td><td></td><td></td><td>60</td></tr>
<tr><td>8</td><td>7PLY/V-2</td><td>53</td><td>54</td><td></td><td></td></tr>
<tr><td>9</td><td>7PLY/200 LBS</td><td>59</td><td>59</td><td></td><td></td></tr>
<tr><td>10</td><td>7PLY/180 LBS</td><td>62.05</td><td></td><td></td><td></td></tr>
<tr><td>11</td><td>7 PLY /240 LBS</td><td></td><td>64</td><td>54</td><td></td></tr>
<tr><td>12</td><td>7 PLY /250 LBS</td><td>66</td><td>66</td><td></td><td></td></tr>
<tr><td>13</td><td>7 PLY /275 LBS</td><td>68</td><td>68</td><td>62</td><td></td></tr>
<tr><td>14</td><td>7 PLY/350 LBS</td><td></td><td>80</td><td></td><td></td></tr>
<tr><td>15</td><td>9PLY</td><td>68</td><td></td><td></td><td></td></tr>

</table>
</div>

</div>

</div>

<script>

/* SERIAL */
function updateSerial(){
let table=document.getElementById("resultTable");
for(let i=1;i<table.rows.length;i++){
table.rows[i].cells[0].innerText=i;
}
}

/* CALC */
function calculate(){
let L=+l.value||0;
let W=+w.value||0;
let H=+h.value||0;
let rate=+rateInput.value||0;
let print=+printInput.value||0;
let type=typeSelect.value;

if(!L||!W||!H||!rate){alert("Fill all values");return;}

let sumLW=L+W;
let result= type==="normal"
? (sumLW>36?((sumLW+2)*2)*(W+H+1):((sumLW*2)+2)*(W+H+1))/1550*rate
: (sumLW<=36?((H+2*W+1)*(2*(L+W)+2)):((L+W+2)*2*(2*W+H+1)))/1550*rate;

let total=result+print;
resultBox.innerText="₹ "+total.toFixed(2);

let row=resultTable.insertRow(1);
row.innerHTML=`<td></td><td>${type}</td><td>${L}×${W}×${H}</td><td>${rate}</td><td>${print}</td><td>${total.toFixed(2)}</td>
<td><button onclick="this.parentElement.parentElement.remove();updateSerial();">X</button></td>`;
updateSerial();
}

/* CLEAR */
function clearData(){
["l","w","h","rate","print"].forEach(id=>document.getElementById(id).value="");
resultBox.innerText="₹ 0";
}

/* EXPORT */
function exportExcel(){
let csv="No,Type,Size,Rate,Print,Total\n";
document.querySelectorAll("#resultTable tr").forEach((r,i)=>{
if(i>0){
let c=r.cells;
csv+=`${c[0].innerText},${c[1].innerText},${c[2].innerText},${c[3].innerText},${c[4].innerText},${c[5].innerText}\n`;
}});
let a=document.createElement("a");
a.href=URL.createObjectURL(new Blob(["\uFEFF"+csv]));
a.download="box_data.csv";
a.click();
}

/* PLY DROPDOWN */
function loadPlyDropdown(){
let t=document.querySelector(".rate-scroll table");
ply.innerHTML='<option value="">Select Ply</option>';
for(let i=1;i<t.rows.length;i++){
let name=t.rows[i].cells[1].innerText;
if(name){
let opt=new Option(name,i);
ply.add(opt);
}
}
}

function setRateFromPly(){
let row=ply.value;
if(row){
let t=document.querySelector(".rate-scroll table");
let val=t.rows[row].cells[2].innerText;
if(val) rate.value=val;
}
}

/* EDIT */
function enableEdit(){
document.querySelectorAll(".rate-scroll td").forEach((c,i)=>{
if(i%6>1){
c.contentEditable=true;
c.style.background="#fff3cd";
}});
}

/* SAVE */
function saveRates(){
let t=document.querySelector(".rate-scroll table");
let data=[];
for(let i=1;i<t.rows.length;i++){
let row=[];
for(let j=0;j<6;j++){
row.push(t.rows[i].cells[j].innerText);
t.rows[i].cells[j].contentEditable=false;
t.rows[i].cells[j].style.background="";
}
data.push(row);
}
localStorage.setItem("rates",JSON.stringify(data));
alert("Saved");
loadPlyDropdown();
}

/* LOAD */
window.onload=function(){
let saved=JSON.parse(localStorage.getItem("rates"));
if(saved){
let t=document.querySelector(".rate-scroll table");
for(let i=1;i<t.rows.length;i++){
for(let j=0;j<6;j++){
t.rows[i].cells[j].innerText=saved[i-1][j];
}
}
}
loadPlyDropdown();
}

/* SHORTCUT IDs */
const l=document.getElementById("l");
const w=document.getElementById("w");
const h=document.getElementById("h");
const rateInput=document.getElementById("rate");
const printInput=document.getElementById("print");
const typeSelect=document.getElementById("type");
const resultBox=document.getElementById("result");
const resultTable=document.getElementById("resultTable");
const ply=document.getElementById("ply");

</script>

</body>
</html>
