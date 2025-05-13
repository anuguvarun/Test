function getFormValues(card: FormGroup) {
  return {
    quantity: card.get('quantity'),
    amount: card.get('amount'),
    shareToggle: card.get('shareToggle')?.value,
    actionToggle: card.get('actionToggle')?.value
  };
}

function getMatchedPosition(accountPositions: AccountPositionsResponseInfo, symbol: string) {
  const accounts = accountPositions.accountPositions ?? [];
  return accounts[0]?.positions?.find((position) => position.symbol === symbol);
}

function sellShareValidate(card: FormGroup, symbol: string, accountPositions: AccountPositionsResponseInfo): void {
  const { quantity, shareToggle, actionToggle } = getFormValues(card);
  
  if (actionToggle === ActionType.Sell && shareToggle === UnitType.Shares) {
    const matchedPosition = getMatchedPosition(accountPositions, symbol);
    
    quantity.setValidators([
      Validators.required,
      matchedPosition?.quantity == null ? Validators.max(0) : Validators.max(matchedPosition.quantity),
    ]);
  }
}

function sellAmountValidate(card: FormGroup, symbol: string, accountPositions: AccountPositionsResponseInfo): void {
  const { amount, shareToggle, actionToggle } = getFormValues(card);

  if (actionToggle === ActionType.Sell && shareToggle === UnitType.Dollars) {
    const matchedPosition = getMatchedPosition(accountPositions, symbol);

    amount.setValidators([
      Validators.required,
      matchedPosition?.marketValue == null
        ? amountValidator(0)
        : amountValidator(matchedPosition.marketValue * 0.9),
    ]);
  }
}