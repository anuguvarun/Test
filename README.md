addCard(): void {
  this.tradeAdded.emit();
  const card = this.presenter.createForm();
  this.cards.push(card);
  this.removals = [];

  this.setupDuplicateValidatorPropagation();

  const searchControl = card.get('search');
  if (searchControl) {
    searchControl.valueChanges.subscribe(() => {
      this.setupDuplicateValidatorPropagation(); // Rebind validators on all cards
    });
  }
}

private setupDuplicateValidatorPropagation(): void {
  this.cards.forEach((card: FormGroup) => {
    const search = card.get('search');
    if (search) {
      const composedValidator = Validators.compose([
        this.search.validator,
        duplicateSearchValidator(() => this.cards)
      ]);
      search.setValidators(composedValidator);
      search.updateValueAndValidity({ emitEvent: false });
    }
  });
}

onConfirmRemove(index: number): void {
  this.cards = this.cards.slice(0, index).concat(this.cards.slice(index + 1));
  delete this.errors[index];
  delete this.removals[index];
  this.removeCard.emit(index);

  if (this.cards.length === 0) {
    this.addCard();
  }

  this.calculateAdjustedBalance();

  this.cards.forEach(card => {
    const search = card.get('search');
    if (search) {
      search.updateValueAndValidity();
    }
  });

  this.setupDuplicateValidatorPropagation(); // Rebind after remove
}