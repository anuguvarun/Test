const createGroup = (search: any, tradeType: any = 'BUY'): FormGroup =>
  new FormGroup({
    search: new FormControl(search),
    tradeType: new FormControl(tradeType)
  });