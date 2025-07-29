addCard(): void {
  this.tradeAdded.emit();

  const newCard = this.presenter.createForm();

  // ✅ Add the validator here — now you can pass this.cards
  newCard.get('search')?.setValidators([
    duplicateSearchValidator(() => this.cards)
  ]);
  newCard.get('search')?.updateValueAndValidity(); // <- triggers it immediately

  this.cards.push(newCard);
  this.removals = [];

  const triggerSiblings = () => {
    this.cards.forEach(card => {
      if (card !== newCard) {
        card.get('search')?.updateValueAndValidity({ onlySelf: true });
      }
    });
  };

  newCard.get('search')?.valueChanges.subscribe(triggerSiblings);
  newCard.get('tradeType')?.valueChanges.subscribe(triggerSiblings);
}