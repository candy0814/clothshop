<!DOCTYPE html>
<html lang="zh-Hant">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>ClothShop ZIP 生成器</title>
</head>
<body>
<h2>一鍵生成 ClothShop 完整 ZIP</h2>
<button id="generate">生成並下載 ZIP</button>

<script src="https://cdnjs.cloudflare.com/ajax/libs/jszip/3.11.0/jszip.min.js"></script>
<script>
document.getElementById('generate').onclick = async function() {
    const zip = new JSZip();
    const folder = zip.folder("clothshop");
    const images = folder.folder("images");

    // index.html
    folder.file("index.html", `<!doctype html>
<html lang="zh-Hant">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>振傑衣舖 — 線上商店</title>
<link rel="stylesheet" href="styles.css">
</head>
<body>
<header class="site-header">
<h1>振傑衣舖</h1>
<nav>
<button id="view-shop">商店</button>
<button id="view-cart">購物車 (<span id="cart-count">0</span>)</button>
</nav>
</header>
<main id="main">
<section id="shop-view" class="view">
<h2>商品列表</h2>
<div id="products" class="products-grid"></div>
</section>
<section id="cart-view" class="view" style="display:none;">
<h2>你的購物車</h2>
<div id="cart-items"></div>
<div id="cart-summary"></div>
<button id="checkout-btn" disabled>前往結帳</button>
<button id="clear-cart">清空購物車</button>
</section>
<section id="checkout-view" class="view" style="display:none;">
<h2>結帳</h2>
<form id="order-form">
<label>收件人姓名<input type="text" id="customer-name" required></label>
<label>電話<input type="tel" id="customer-phone" required></label>
<label>電子郵件<input type="email" id="customer-email" required></label>
<label>地址<textarea id="customer-address" rows="2" required></textarea></label>
<label>付款方式
<select id="payment-method">
<option value="cod">貨到付款</option>
<option value="bank">銀行轉帳</option>
<option value="manual">線上／其他</option>
</select>
</label>
<button type="submit">下訂單</button>
<button type="button" id="back-to-cart">返回購物車</button>
</form>
</section>
</main>
<script src="product.js"></script>
</body>
</html>`);

    // admin.html
    folder.file("admin.html", `<!doctype html>
<html lang="zh-Hant">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>後台管理 - 振傑衣舖</title>
<link rel="stylesheet" href="styles.css">
</head>
<body>
<header><h1>振傑衣舖後台管理</h1></header>
<main>
<section id="login-section">
<h2>請登入</h2>
<label>密碼<input type="password" id="admin-pass"></label>
<button id="login-btn">登入</button>
<p id="login-msg" style="color:red"></p>
</section>
<section id="admin-section" style="display:none;">
<h2>商品管理</h2>
<button id="add-new">新增商品</button>
<div id="product-list"></div>
</section>
</main>
<script src="admin.js"></script>
</body>
</html>`);

    // styles.css
    folder.file("styles.css", `body{font-family:sans-serif;margin:0;padding:0;background:#f5f5f5;}
.site-header{background:#333;color:white;padding:10px;}
.site-header h1{margin:0;}
.nav-btn{margin-left:10px;}
.products-grid{display:flex;flex-wrap:wrap;gap:15px;}
.card{background:white;padding:15px;border-radius:10px;box-shadow:0 4px 8px rgba(0,0,0,0.1);width:220px;transition:0.3s;}
.card:hover{transform:translateY(-5px);box-shadow:0 6px 12px rgba(0,0,0,0.15);}
.card img{width:100%;border-radius:8px;}
.card h3{margin:5px 0;}
.card p.description{font-size:0.9em;color:#555;height:40px;overflow:hidden;}
.card select{width:100%;padding:5px;margin-bottom:5px;}
.btn{padding:5px 10px;border:none;border-radius:4px;cursor:pointer;}
.btn.primary{background:#007bff;color:white;}
.view{padding:10px;}
input, textarea, select{width:100%;margin-bottom:5px;padding:5px;}`);

    // product.js
    folder.file("product.js", `let products=[{id:1,name:"黑色T恤",price:350,image:"images/tshirt_black.svg",description:"簡約黑色T恤，百搭舒適",sizes:["S","M","L","XL"]},{id:2,name:"藍色外套",price:800,image:"images/jacket.svg",description:"時尚藍色外套，防風保暖",sizes:["S","M","L","XL"]},{id:3,name:"綠色長褲",price:600,image:"images/pants.svg",description:"綠色長褲，休閒百搭",sizes:["S","M","L","XL"]}];
let cart=[];
function displayProducts(){const productsDiv=document.getElementById("products");productsDiv.innerHTML="";products.forEach(p=>{const card=document.createElement("div");card.className="card";const sizeOptions=p.sizes.map(s=>\`<option value="\${s}">\${s}</option>\`).join("");card.innerHTML=\`<img src="\${p.image}"><h3>\${p.name}</h3><p class="description">\${p.description}</p><p>價格: $\${p.price}</p><select>\${sizeOptions}</select><button class="btn" onclick="addToCart(\${p.id})">加入購物車</button>\`;productsDiv.appendChild(card);});}
function addToCart(id){const product=products.find(p=>p.id===id);const exist=cart.find(c=>c.id===id);if(exist) exist.qty+=1; else cart.push({...product,qty:1});updateCart();}
function updateCart(){const cartItemsDiv=document.getElementById("cart-items");const cartSummaryDiv=document.getElementById("cart-summary");const cartCountSpan=document.getElementById("cart-count");cartItemsDiv.innerHTML="";cart.forEach(item=>{const div=document.createElement("div");div.innerHTML=\`\${item.name} x\${item.qty} - $\${item.price*item.qty} <button onclick="removeFromCart(\${item.id})">移除</button>\`;cartItemsDiv.appendChild(div);});const total=cart.reduce((sum,i)=>sum+i.price*i.qty,0);cartSummaryDiv.innerHTML=\`總計: $\${total}\`;cartCountSpan.textContent=cart.reduce((sum,i)=>sum+i.qty,0);document.getElementById("checkout-btn").disabled=cart.length===0;}
function removeFromCart(id){cart=cart.filter(i=>i.id!==id);updateCart();}
displayProducts();updateCart();`);

    // admin.js
    folder.file("admin.js", `const adminPass="joe0814";const loginSection=document.getElementById("login-section");const adminSection=document.getElementById("admin-section");const loginBtn=document.getElementById("login-btn");const loginMsg=document.getElementById("login-msg");const productListDiv=document.getElementById("product-list");const addNewBtn=document.getElementById("add-new");
let products=[{id:1,name:"黑色T恤",price:350,image:"images/tshirt_black.svg",description:"簡約黑色T恤，百搭舒適",sizes:["S","M","L","XL"]},{id:2,name:"藍色外套",price:800,image:"images/jacket.svg",description:"時尚藍色外套，防風保暖",sizes:["S","M","L","XL"]},{id:3,name:"綠色長褲",price:600,image:"images/pants.svg",description:"綠色長褲，休閒百搭",sizes:["S","M","L","XL"]}];
loginBtn.onclick=()=>{const val=document.getElementById("admin-pass").value;if(val===adminPass){loginSection.style.display="none";adminSection.style.display="block";displayAdminProducts();} else loginMsg.textContent="密碼錯誤";}
function displayAdminProducts(){productListDiv.innerHTML="";products.forEach(p=>{const div=document.createElement("div");div.innerHTML=\`\${p.name} - $\${p.price} <button onclick="editProduct(\${p.id})">修改</button> <button onclick="deleteProduct(\${p.id})">刪除</button>\`;productListDiv.appendChild(div);});}
addNewBtn.onclick=()=>{const name=prompt("商品名稱");const price=parseInt(prompt("價格"));const description=prompt("商品介紹");if(!name||!price||!description)return;const fileInput=document.createElement("input");fileInput.type="file";fileInput.accept="image/*";fileInput.onchange=()=>{const file=fileInput.files[0];const reader=new FileReader();reader.onload=function(e){const sizes=["S","M","L","XL"];const id=Math.max(...products.map(p=>p.id))+1;products.push({id,name,price,image:e.target.result,description,sizes});displayAdminProducts();};reader.readAsDataURL(file);};fileInput.click();}
function editProduct(id){const p=products.find(p=>p.id===id);const name=prompt("修改名稱",p.name);const price=parseInt(prompt("修改價格",p.price));const description=prompt("修改介紹",p.description);if(name&&price&&description){const changeImg=confirm("是否要更換圖片？");if(changeImg){const fileInput=document.createElement("input");fileInput.type="file";fileInput.accept="image/*";fileInput.onchange=()=>{const file=fileInput.files[0];const reader=new FileReader();reader.onload=function(e){p.name=name;p.price=price;p.description=description;p.image=e.target.result;displayAdminProducts();};reader.readAsDataURL(file);};fileInput.click();} else {p.name=name;p.price=price;p.description=description;displayAdminProducts();}}}
function deleteProduct(id){if(confirm("確定要刪除嗎？")){products=products.filter(p=>p.id!==id);displayAdminProducts();}}`);

    // 範例圖片
    images.file("tshirt_black.svg", '<svg xmlns="http://www.w3.org/2000/svg" width="100" height="100"><rect width="100" height="100" fill="gray"/></svg>');
    images.file("jacket.svg", '<svg xmlns="http://www.w3.org/2000/svg" width="100" height="100"><rect width="100" height="100" fill="blue"/></svg>');
    images.file("pants.svg", '<svg xmlns="http://www.w3.org/2000/svg" width="100" height="100"><rect width="100" height="100" fill="green"/></svg>');

    // 生成 ZIP 並下載
    const content = await zip.generateAsync({type:"blob"});
    const a = document.createElement("a");
    a.href = URL.createObjectURL(content);
    a.download = "clothshop_full.zip";
    a.click();
};
</script>
</body>
</html>
