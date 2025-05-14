const form = new FormGroup({
  shareToggle: new FormControl(UnitType.Shares),
  actionToggle: new FormControl(ActionType.Sell),
  quantity: new FormControl(),
  amount: new FormControl(),
});