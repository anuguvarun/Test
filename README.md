setupDuplicateSubscriptions(): void {
  this.cards.forEach((formGroup, index) => {
    const control = formGroup.get('search');
    control?.valueChanges.subscribe(() => {
      this.validationTrigger++; // Triggers all children to re-validate
    });
  });
}