private _accountPositions: any;
@Input()
set accountPositions(value: any) {
  this._accountPositions = value;

  const positions = value?.accountPositions?.[0]?.positions;
  if (positions) {
    const safe = positions.map(p => ({ symbol: p.symbol, quantity: p.quantity }));
    console.log('Safe preview:', safe);
  }
}