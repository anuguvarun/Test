triggerDuplicateValidation(changedIndex: number) {
  this.cards.controls.forEach((card, index) => {
    if (index !== changedIndex) {
      card.get('search')?.updateValueAndValidity({ onlySelf: true });
      card.get('tradeType')?.updateValueAndValidity({ onlySelf: true });
    }
  });
}



this.cards.controls.forEach((card: FormGroup, index: number) => {
  card.get('search')?.valueChanges.subscribe(() => {
    this.triggerDuplicateValidation(index);
  });

  card.get('tradeType')?.valueChanges.subscribe(() => {
    this.triggerDuplicateValidation(index);
  });
});