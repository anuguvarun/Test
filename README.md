private updateUiBasedOnActionAndCusip(): void {
  if (this.actionToggle.value === ActionType.Sell) {
    const matchedPosition = this.presenter.getMatchedPosition(
      this.accountPositions,
      this.cusip.value
    );

    const isCusipNull = matchedPosition?.cusip == null;

    this.showUnitToggle = !isCusipNull;
    this.showAmountOrQuantityField = !isCusipNull;
  } else {
    this.showUnitToggle = true;
    this.showAmountOrQuantityField = true;
  }
}