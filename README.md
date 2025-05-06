const safeData = this.accountPositions?.accountPositions?.map(p => ({
  brokerageAccountNumber: p.brokerageAccountNumber,
  positions: p.positions?.map(pos => ({
    symbol: pos.symbol,
    quantity: pos.quantity
  }))
}));
console.log('Safe subset:', safeData);