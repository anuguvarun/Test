@Input() cardValues: any[] = [];

buyCards: any[] = [];
sellCards: any[] = [];

constructor(public readonly orderStepPresenter: OrderStepsPresenter) {}

ngOnChanges(): void {
  if (Array.isArray(this.cardValues)) {
    this.processCardValues();
  }
}

processCardValues(): void {
  this.buyCards = this.cardValues.filter(
    (card) => card?.value?.actionToggle === ActionType.Buy
  );
  this.sellCards = this.cardValues.filter(
    (card) => card?.value?.actionToggle === ActionType.Sell
  );
  this.orderStepPresenter.getCardInfoByAction(this.cardValues);
}