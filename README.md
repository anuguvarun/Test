addCard(): void {
  const newCard = this.presenter.createForm(); // no change here
  this.cards.push(newCard);

  const revalidateSiblings = () => {
    this.cards.forEach(card => {
      if (card !== newCard) {
        card.get('search')?.updateValueAndValidity({ onlySelf: true });
      }
    });
  };

  newCard.get('search')?.setValidators(duplicateSearchValidator(() => this.cards));
  newCard.get('search')?.updateValueAndValidity(); // ensure it runs once
  newCard.get('search')?.valueChanges.subscribe(revalidateSiblings);
  newCard.get('tradeType')?.valueChanges.subscribe(revalidateSiblings);

  this.removals = [];
}