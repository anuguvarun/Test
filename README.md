addCard(): void {
  this.tradeAdded.emit();

  const newCard = this.presenter.createForm();
  this.cards.push(newCard);  // cards is an array of FormGroup
  this.removals = [];

  // ✅ Revalidate siblings when this card's values change
  const searchControl = newCard.get('search');
  const tradeTypeControl = newCard.get('tradeType');

  const triggerRevalidation = () => {
    this.cards.forEach(card => {
      if (card !== newCard) {
        card.get('search')?.updateValueAndValidity({ onlySelf: true });
      }
    });
  };

  searchControl?.valueChanges.subscribe(triggerRevalidation);
  tradeTypeControl?.valueChanges.subscribe(triggerRevalidation);
}