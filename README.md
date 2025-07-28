refreshCardValidators(): void {
  this.cards.forEach((card, index) => {
    const search = card.get('search');
    if (!search) return;

    // Extract current validators (flattened)
    const currentValidators = (search.validator ? [search.validator] : []);

    // Append updated duplicate validator
    const duplicateValidator = duplicateSearchValidator(index, this.cards);

    // Compose all validators again
    const composed = Validators.compose([...currentValidators, duplicateValidator]);

    search.setValidators(composed);
    search.updateValueAndValidity({ onlySelf: true });
  });
}