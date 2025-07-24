setupDuplicateSubscriptions(): void {
  this.cards.forEach((formGroup, index) => {
    const control = formGroup.get('search');
    if (!control) return;

    control.valueChanges.subscribe(() => {
      this.revalidateAllCards(); // Trigger validator refresh
    });
  });
}