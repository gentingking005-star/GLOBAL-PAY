# GLOBAL-PAY
Global Pay - Multi-currency Online Payment Website

<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>Global Pay</title>
<style>
*{box-sizing:border-box}
body{margin:0;background:#f4f6fa;color:#17243a;font-family:Arial,sans-serif}
header{height:70px;background:#101c34;color:white;padding:0 6%;display:flex;align-items:center;justify-content:space-between}
.logo{font-size:24px;font-weight:bold;letter-spacing:1px}
main{max-width:1000px;margin:35px auto;padding:15px}
.card{background:white;border-radius:13px;padding:25px;margin-bottom:20px;box-shadow:0 3px 18px #17243a0a}
h2,h3{margin-top:0}
.muted{color:#8490a3;font-size:13px}
.amount{font-size:38px;font-weight:bold;margin:15px 0}
input,select{width:100%;padding:13px;border:1px solid #dce2eb;border-radius:8px;margin:8px 0 17px;background:white}
button{border:0;border-radius:8px;padding:12px 17px;cursor:pointer;font-size:14px}
.primary{background:#245bd8;color:white}
.secondary{background:#edf1f7;color:#25334b}
.full{width:100%}
.grid{display:grid;grid-template-columns:repeat(2,1fr);gap:12px}
.method{background:white;border:1px solid #dce2eb;text-align:left;padding:17px}
.method.active{border:2px solid #245bd8;background:#f3f6ff}
.hidden{display:none!important}
.tag{padding:5px 9px;border-radius:20px;font-size:12px}
.ok{background:#e2f6e9;color:#167746}
.wait{background:#fff1d5;color:#966300}
.bad{background:#ffe7e7;color:#bd3333}
.statgrid{display:grid;grid-template-columns:repeat(3,1fr);gap:12px}
.stat{background:#f3f5f9;padding:17px;border-radius:9px}
.stat strong{display:block;font-size:24px;margin-top:8px}
table{width:100%;border-collapse:collapse;min-width:570px}
th,td{padding:13px 8px;text-align:left;border-bottom:1px solid #edf0f4;font-size:13px}
.link{padding:14px;background:#f1f5ff;border-radius:8px;overflow-wrap:anywhere;margin:12px 0}
.notice{padding:12px;background:#fff6df;color:#765a1e;border-radius:8px;font-size:12px;line-height:1.6}
footer{text-align:center;padding:25px;color:#8993a5;font-size:12px}
@media(max-width:600px){
 .statgrid{grid-template-columns:1fr 1fr}
 .card{padding:18px}
 header{padding:0 18px}
}
</style>
</head>
<body>

<header>
 <div class="logo">GLOBAL PAY</div>
 <button class="secondary" onclick="showAdmin()">ADMIN</button>
</header>

<main>

<!-- 客户支付页面 -->
<section id="checkout" class="card hidden">
 <p class="muted">GLOBAL PAY / SECURE CHECKOUT</p>
 <h2>Secure Payment</h2>
 <p class="muted">Complete your payment using your preferred method.</p>

 <div class="amount">$200.00 USD</div>
 <p class="muted">Fixed payment amount · Reusable payment link</p>

 <label>Choose payment currency</label>
 <select id="currency">
  <option value="USD">USD - US Dollar</option>
  <option value="EUR">EUR - Euro</option>
  <option value="GBP">GBP - British Pound</option>
  <option value="CNY">CNY - Chinese Yuan</option>
  <option value="MYR">MYR - Malaysian Ringgit</option>
  <option value="SGD">SGD - Singapore Dollar</option>
  <option value="AUD">AUD - Australian Dollar</option>
  <option value="CAD">CAD - Canadian Dollar</option>
  <option value="JPY">JPY - Japanese Yen</option>
  <option value="HKD">HKD - Hong Kong Dollar</option>
  <option value="AED">AED - UAE Dirham</option>
  <option value="INR">INR - Indian Rupee</option>
  <option value="THB">THB - Thai Baht</option>
  <option value="Other">Other supported currency</option>
 </select>

 <p class="muted">Payment method</p>
 <div class="grid">
  <button class="method active" onclick="choose(this,'Card')">💳 Bank Card / Credit Card</button>
  <button class="method" onclick="choose(this,'PayPal')">PayPal</button>
  <button class="method" onclick="choose(this,'Google Pay')">Google Pay</button>
  <button class="method" onclick="choose(this,'Apple Pay')">Apple Pay</button>
  <button class="method" onclick="choose(this,'Other Pay')">Other Local Pay</button>
 </div>

 <br>
 <button class="primary full" onclick="customerPay()">Continue to Payment</button>
 <p id="customerStatus" class="muted"></p>
 <div class="notice">
  DEMO VERSION. No real payment is processed. Live currency conversion and payment methods will be provided by the approved payment processor.
 </div>
</section>

<!-- 管理员登录 -->
<section id="login" class="card hidden">
 <h2>Administrator Login</h2>
 <p class="muted">Global Pay Management</p>
 <label>Administrator Password</label>
 <input type="password" id="password" placeholder="Enter password">
 <button class="primary full" onclick="login()">Login</button>
 <p id="loginError" class="muted"></p>
 <p class="muted">Demo password: gp2026</p>
</section>

<!-- 管理员后台 -->
<section id="dashboard" class="hidden">

 <div class="card">
  <h2>Dashboard</h2>
  <div class="statgrid">
   <div class="stat">Total Orders<strong id="total">0</strong></div>
   <div class="stat">Successful<strong id="success">0</strong></div>
   <div class="stat">Pending<strong id="pending">0</strong></div>
  </div>
 </div>

 <!-- 通用支付链接 -->
 <div class="card">
  <h3>Payment Link</h3>
  <p class="muted">固定金额：200 USD · 无限次使用</p>
  <div class="link" id="paymentLink"></div>
  <button class="primary" onclick="copyLink()">Copy Payment Link</button>
 </div>

 <!-- 支付订单 -->
 <div class="card">
  <h3>Payment Transactions</h3>
  <button class="secondary" onclick="exportCSV()">Export CSV</button>
  <div style="overflow-x:auto;margin-top:15px">
   <table>
    <thead>
     <tr>
      <th>Order ID</th>
      <th>Currency</th>
      <th>Settlement USD</th>
      <th>Method</th>
      <th>Status</th>
     </tr>
    </thead>
    <tbody id="records"></tbody>
   </table>
  </div>
 </div>

 <!-- 虚拟卡连接 -->
 <div class="card">
  <h3>Virtual Card Provider</h3>
  <p class="muted">虚拟卡运营商 API 连接状态</p>
  <p id="providerStatus"><span class="tag wait">Not Connected</span></p>
  <label>Provider Name</label>
  <input id="providerName" placeholder="Provider name">
  <button class="primary" onclick="saveProvider()">Save Provider Configuration</button>
  <p class="muted">正式 API 密钥和商户凭证须由服务器安全保存，不在浏览器中填写。</p>
 </div>

 <!-- 提款 -->
 <div class="card">
  <h3>Withdraw to Virtual Card</h3>
  <p class="muted">Settlement currency: USD</p>
  <p>Available balance: <strong id="balance">$0.00</strong></p>
  <label>Withdrawal Amount (USD)</label>
  <input id="withdrawAmount" type="number" min="0.01" step="0.01" placeholder="Enter amount">
  <button class="primary" onclick="withdraw()">Submit Withdrawal</button>
  <p id="withdrawStatus" class="muted"></p>
  <h3>Withdrawal Records</h3>
  <div id="withdrawRecords"></div>
 </div>

 <button class="secondary" onclick="logout()">Logout</button>
</section>

</main>

<footer>
 GLOBAL PAY © 2026 · Secure Payment Interface
</footer>

<script>
const STORAGE='globalpay_demo_v3';
const WSTORAGE='globalpay_withdraw_demo_v3';
let method='Card';
let withdrawals=[];
let provider='';

function loadOrders(){
 try{return JSON.parse(localStorage.getItem(STORAGE)||'[]')}
 catch(e){return []}
}
function saveOrders(data){
 localStorage.setItem(STORAGE,JSON.stringify(data));
}
function page(id){
 ['checkout','login','dashboard'].forEach(x=>
  document.getElementById(x).classList.add('hidden'));
 document.getElementById(id).classList.remove('hidden');
}
function link(){
 return location.origin+location.pathname+'?pay=global200';
}
function showAdmin(){
 page('login');
}
function login(){
 if(document.getElementById('password').value==='gp2026'){
  page('dashboard');
  render();
 }else{
  document.getElementById('loginError').textContent='Incorrect password';
 }
}
function logout(){
 page('checkout');
}
function choose(el,name){
 method=name;
 document.querySelectorAll('.method').forEach(x=>x.classList.remove('active'));
 el.classList.add('active');
}
function customerPay(){
 let currency=document.getElementById('currency').value;
 let order={
  id:'GP'+Date.now(),
  amountUSD:200,
  currency,
  method,
  status:'pending',
  created:new Date().toLocaleString()
 };
 let all=loadOrders();
 all.unshift(order);
 saveOrders(all);

 document.getElementById('customerStatus').textContent=
 'Order '+order.id+' created. Awaiting payment provider confirmation.';

 // Demo only: never claim a real payment succeeded.
 if(location.protocol==='file:'){
  alert('演示订单已创建。正式支付需要部署网站及服务器。');
 }
}
function copyLink(){
 let url=link();
 if(navigator.clipboard){
  navigator.clipboard.writeText(url).then(()=>alert('Payment link copied'));
 }else{
  prompt('Copy payment link',url);
 }
}
function render(){
 let all=loadOrders();
 document.getElementById('total').textContent=all.length;
 document.getElementById('success').textContent=
 all.filter(x=>x.status==='success').length;
 document.getElementById('pending').textContent=
 all.filter(x=>x.status==='pending').length;
 document.getElementById('paymentLink').textContent=link();

 document.getElementById('records').innerHTML=all.map(x=>{
  let cls=x.status==='success'?'ok':x.status==='failed'?'bad':'wait';
  return `<tr>
   <td>${escapeHTML(x.id)}</td>
   <td>${escapeHTML(x.currency)}</td>
   <td>$${Number(x.amountUSD).toFixed(2)}</td>
   <td>${escapeHTML(x.method)}</td>
   <td><span class="tag ${cls}">${escapeHTML(x.status)}</span></td>
  </tr>`;
 }).join('');

 renderWithdrawals();
}
function saveProvider(){
 let name=document.getElementById('providerName').value.trim();
 if(!name){alert('Enter provider name');return}
 provider=name;
 document.getElementById('providerStatus').innerHTML=
 '<span class="tag wait">Configuration Saved - Not Verified</span>';
}
function withdraw(){
 if(!provider){
  alert('Please configure a provider first');
  return;
 }
 let amount=Number(document.getElementById('withdrawAmount').value);
 if(!Number.isFinite(amount)||amount<=0){
  alert('Enter a valid amount');
  return;
 }
 withdrawals=JSON.parse(localStorage.getItem(WSTORAGE)||'[]');
 withdrawals.unshift({
  id:'WD'+Date.now(),
  amount,
  status:'DEMO - Not Sent'
 });
 localStorage.setItem(WSTORAGE,JSON.stringify(withdrawals));
 document.getElementById('withdrawStatus').textContent=
 '模拟提款申请已保存，没有发生真实转账。';
 renderWithdrawals();
}
function renderWithdrawals(){
 try{withdrawals=JSON.parse(localStorage.getItem(WSTORAGE)||'[]')}
 catch(e){withdrawals=[]}
 document.getElementById('withdrawRecords').innerHTML=
 withdrawals.map(x=>'<p>'+escapeHTML(x.id)+' · $'+
 Number(x.amount).toFixed(2)+' · '+escapeHTML(x.status)+'</p>').join('');
}
function exportCSV(){
 let rows=[['Order ID','Currency','Settlement USD','Method','Status'],
 ...loadOrders().map(x=>[x.id,x.currency,x.amountUSD,x.method,x.status])];
 let csv=rows.map(r=>r.join(',')).join('\n');
 let a=document.createElement('a');
 a.href=URL.createObjectURL(new Blob(['\ufeff'+csv],{type:'text/csv'}));
 a.download='global-pay-transactions.csv';
 a.click();
}
function escapeHTML(s){
 return String(s).replace(/[&<>"']/g,c=>({
 '&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;',"'":'&#39;'
 }[c]));
}
const params=new URLSearchParams(location.search);
if(params.get('pay')==='global200'){
 page('checkout');
}else{
 page('checkout');
}
</script>
</body>
</html>
