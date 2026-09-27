# lemonade-coffee-
<!DOCTYPE html>
<html>
<head>
  <style>
    body { font-family: Arial, sans-serif; background: #fffde6; padding: 20px; text-align: center; }
    .menu-box, .owner-box { background: white; padding: 20px; border-radius: 12px; max-width: 450px; margin: auto; box-shadow: 0 4px 8px rgba(0,0,0,0.1); text-align: left; display: none; }
    .active { display: block; }
    input, textarea, select { width: 100%; padding: 8px; margin: 5px 0 15px 0; border: 1px solid #ccc; border-radius: 5px; box-sizing: border-box; }
    .item-row { display: flex; justify-content: space-between; align-items: center; margin-bottom: 10px; font-size: 14px; }
    .item-row input { width: 60px; margin: 0; }
    .total-box { font-weight: bold; font-size: 18px; margin: 15px 0; color: #333; text-align: center; background: #fffde6; padding: 10px; border-radius: 5px; }
    button { background: #ffcc00; color: #333; padding: 12px; width: 100%; border: none; border-radius: 5px; font-weight: bold; font-size: 16px; cursor: pointer; margin-top: 5px; }
    button:hover { background: #e6b800; }
    .owner-btn { background: #ddd; font-size: 12px; padding: 6px; width: auto; float: right; }
  </style>
</head>
<body>

  <!-- Customer Order Form -->
  <div id="orderForm" class="menu-box active">
    <button class="owner-btn" onclick="openOwnerView()">Owner Login</button>
    <h2 style="text-align: center; clear: both;">🍋 Lemonade Stand 🍋</h2>
    
    <label for="name">Your Name:</label>
    <input type="text" id="name" placeholder="Type your name">

    <label><b>Choose Your Items:</b></label>
    <div class="item-row"><span>🍋 Lemonade ($1.00)</span><input type="number" id="lemonade" value="0" min="0" oninput="calculateTotal()"></div>
    <div class="item-row"><span>☕ Coffee ($1.50)</span><input type="number" id="coffee" value="0" min="0" oninput="calculateTotal()"></div>
    <div class="item-row"><span>💧 Water (Free)</span><input type="number" id="water" value="0" min="0" oninput="calculateTotal()"></div>
    <div class="item-row"><span>🍪 Snacks ($2.00)</span><input type="number" id="snacks" value="0" min="0" oninput="calculateTotal()"></div>
    <div class="item-row"><span>🍫 Chocolate Milk ($1.50)</span><input type="number" id="choc_milk" value="0" min="0" oninput="calculateTotal()"></div>
    <div class="item-row"><span>🧋 Chocolate Latte ($2.00)</span><input type="number" id="choc_latte" value="0" min="0" oninput="calculateTotal()"></div>

    <div class="total-box" id="totalDisplay">Total: $0.00</div>

    <label for="payment">Payment Method:</label>
    <select id="payment">
      <option value="Cash (IRL)">Cash (Pay in person)</option>
      <option value="Apple Pay">Apple Pay (Send to grown-up)</option>
    </select>

    <label for="notes">Special Instructions:</label>
    <textarea id="notes" placeholder="Write any special requests here..."></textarea>
    
    <button onclick="sendOrder()">Send Order Now</button>
  </div>

  <!-- Owner Dashboard -->
  <div id="ownerDashboard" class="owner-box">
    <h2>🔒 Owner Dashboard</h2>
    <p>Saved Orders:</p>
    <div id="orderList" style="background: #f9f9f9; padding: 10px; border-radius: 5px; max-height: 200px; overflow-y: auto; font-size: 13px;"></div>
    <button onclick="backToForm()" style="background: #ccc;">Back to Menu</button>
  </div>

  <script>
    function calculateTotal() {
      let l = document.getElementById('lemonade').value * 1.00;
      let c = document.getElementById('coffee').value * 1.50;
      let w = document.getElementById('water').value * 0.00;
      let s = document.getElementById('snacks').value * 2.00;
      let cm = document.getElementById('choc_milk').value * 1.50;
      let cl = document.getElementById('choc_latte').value * 2.00;
      
      let total = l + c + w + s + cm + cl;
      document.getElementById('totalDisplay').innerText = "Total: $" + total.toFixed(2);
    }

    function sendOrder() {
      let name = document.getElementById('name').value;
      let payment = document.getElementById('payment').value;
      let notes = document.getElementById('notes').value;
      
      let l = document.getElementById('lemonade').value;
      let c = document.getElementById('coffee').value;
      let w = document.getElementById('water').value;
      let s = document.getElementById('snacks').value;
      let cm = document.getElementById('choc_milk').value;
      let cl = document.getElementById('choc_latte').value;

      let total = (l * 1.00) + (c * 1.50) + (s * 2.00) + (cm * 1.50) + (cl * 2.00);

      let orderText = "Name: " + name + 
        " | Lemonade: " + l + 
        " | Coffee: " + c + 
        " | Water: " + w + 
        " | Snacks: " + s + 
        " | Choc Milk: " + cm + 
        " | Choc Latte: " + cl + 
        " | Total: $" + total.toFixed(2) +
        " | Pay: " + payment +
        " | Notes: " + notes;

      // Save order to local storage
      let orders = JSON.parse(localStorage.getItem('lemonadeOrders') || '[]');
      orders.push(orderText);
      localStorage.setItem('lemonadeOrders', JSON.stringify(orders));

      // Send text message immediately
      window.location.href = "sms:9843147447?body=" + encodeURIComponent("New Order! " + orderText);
    }

    function openOwnerView() {
      let code = prompt("Enter owner code:");
      if (code === "Apple@1a") {
        document.getElementById('orderForm').classList.remove('active');
        document.getElementById('ownerDashboard').classList.add('active');
        
        let orders = JSON.parse(localStorage.getItem('lemonadeOrders') || '[]');
        let listHTML = "";
        if(orders.length === 0) {
          listHTML = "No orders yet.";
        } else {
          orders.forEach((ord, index) => {
            listHTML += "<p><b>Order #" + (index + 1) + "</b><br>" + ord + "</p><hr>";
          });
        }
        document.getElementById('orderList').innerHTML = listHTML;
      } else if (code !== null) {
        alert("Incorrect code!");
      }
    }

    function backToForm() {
      document.getElementById('ownerDashboard').classList.remove('active');
      document.getElementById('orderForm').classList.add('active');
    }
  </script>

</body>
</html>
