<div class="trade-container">
  <div class="trade-header">
    <span class="trade-number">Trade 1 of 3</span>
    <span class="trade-amount">$1,000.00</span>
  </div>
  
  <div class="trade-details">
    <div class="trade-row"><span class="label">Trade type:</span> <span>Mutual Fund</span></div>
    <div class="trade-row"><span class="label">Security:</span> <span>SPAXX<br>Fidelity Government Money Market Fund</span></div>
    <div class="trade-row"><span class="label">Last price:</span> <span>$1.00</span></div>
    <div class="trade-row"><span class="label">Action:</span> <span>Buy</span></div>
    <div class="trade-row"><span class="label">Type:</span> <span>Dollars</span></div>
    <div class="trade-row"><span class="label">Amount:</span> <span>$1,000.00</span></div>
    <div class="trade-row"><span class="label">Fees:</span> <span>Included, if applicable</span></div>
  </div>
</div>

.trade-container {
  border: 1px solid #ccc;
  padding: 16px;
  width: 320px;
  font-family: Arial, sans-serif;
  background-color: #f9f9f9;
  border-radius: 8px;
}

.trade-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  font-weight: bold;
  margin-bottom: 12px;
}

.trade-number {
  font-size: 16px;
}

.trade-amount {
  color: green;
  font-size: 18px;
}

.trade-details .trade-row {
  display: flex;
  justify-content: space-between;
  margin-bottom: 8px;
}

.trade-details .label {
  font-weight: bold;
  margin-right: 8px;
}
