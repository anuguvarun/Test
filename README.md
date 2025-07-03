setActionToggleValidatorsByCusip(): void {
  let validators = [Validators.required];

  if (this.actionToggle.value === ActionType.Sell) {
    const matchedPosition = this.presenter.getMatchedPosition(
      this.accountPositions,
      this.cusip.value
    );

    const isCusipNull = matchedPosition?.cusip == null;

    validators = isCusipNull
      ? [securityHeldValidator(true)]
      : [Validators.required];

    this.showError = false;
  } else {
    this.showError = false;
  }

  this.actionToggle.setValidators(validators);
  this.actionToggle.updateValueAndValidity();
}