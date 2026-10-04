const products = [
  { name: 'Smart Watch', stock: 42, sales: 68, season: 1.45, trend: 1.2, unitCost: 3200 },
  { name: 'Wireless Earbuds', stock: 84, sales: 110, season: 1.3, trend: 1.15, unitCost: 1800 },
  { name: 'Air Fryer', stock: 21, sales: 52, season: 1.7, trend: 1.25, unitCost: 6500 },
  { name: 'Portable Speaker', stock: 58, sales: 90, season: 1.2, trend: 1.1, unitCost: 2400 }
];

const productList = document.getElementById('product-list');
const sendAlertBtn = document.getElementById('send-alert');
const customerSelect = document.getElementById('customer-select');
const assistantInput = document.getElementById('assistant-input');
const askAiBtn = document.getElementById('ask-ai');
const assistantChat = document.getElementById('assistant-chat');

function calculateForecast(product) {
  const demand = Math.round(product.sales * product.season * product.trend);
  const reorder = Math.max(0, demand - product.stock);
  const status = reorder > 30 ? 'low' : 'good';
  return { demand, reorder, status };
}

function renderProducts() {
  productList.innerHTML = '';

  products.forEach((product) => {
    const forecast = calculateForecast(product);
    const row = document.createElement('div');
    row.className = 'product-row';

    row.innerHTML = `
      <div>
        <div class="product-name">${product.name}</div>
        <div class="product-meta">Avg sales: ${product.sales}/mo</div>
      </div>
      <div>
        <div class="product-meta">Stock</div>
        <div class="product-name">${product.stock}</div>
      </div>
      <div>
        <div class="product-meta">Demand</div>
        <div class="product-name">${forecast.demand}</div>
      </div>
      <div class="stock-pill ${forecast.status}">${forecast.status === 'low' ? 'Reorder' : 'Stable'}</div>
      <div class="reorder">+${forecast.reorder} units</div>
    `;

    productList.appendChild(row);
  });
}

sendAlertBtn.addEventListener('click', () => {
  const customer = customerSelect.value;
  const product = products[0].name;

  const alertMessage = `Hi ${customer}! ${product} is back in stock and available for fast delivery with a seasonal offer.`;
  alert(`WhatsApp alert sent to ${customer}:\n\n${alertMessage}`);
});

function addAssistantMessage(text, isUser = false) {
  const message = document.createElement('div');
  message.className = `assistant-msg ${isUser ? 'user' : ''}`;
  message.textContent = text;
  assistantChat.appendChild(message);
  assistantChat.scrollTop = assistantChat.scrollHeight;
}

askAiBtn.addEventListener('click', () => {
  const query = assistantInput.value.trim();

  if (!query) return;

  addAssistantMessage(query, true);
  assistantInput.value = '';

  let response = 'I can help with product availability, restocking, and demand forecasts.';

  if (query.toLowerCase().includes('stock') || query.toLowerCase().includes('available')) {
    response = 'The Smart Watch is low in stock. We recommend reordering 96 units before the next seasonal spike.';
  } else if (query.toLowerCase().includes('watch')) {
    response = 'The Smart Watch is trending upward by 38% this season. It is a strong product to reorder now.';
  } else if (query.toLowerCase().includes('price')) {
    response = 'Current pricing is competitive, and seasonal discounts are active on selected products this week.';
  } else if (query.toLowerCase().includes('earbud') || query.toLowerCase().includes('speaker')) {
    response = 'Wireless Earbuds and Portable Speaker are performing steadily with healthy stock and consistent repeat demand.';
  }

  setTimeout(() => addAssistantMessage(response), 300);
});

assistantInput.addEventListener('keydown', (event) => {
  if (event.key === 'Enter') {
    askAiBtn.click();
  }
});

document.querySelector('.lead-form').addEventListener('submit', (event) => {
  event.preventDefault();
  alert('Thank you! Our team will contact you shortly to plan your retail AI setup.');
  event.target.reset();
});

renderProducts();
