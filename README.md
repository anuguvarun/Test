private duplicateSubscriptions: Subscription[] = [];

private setupDuplicateValueChangeSubscriptions(): void {
  // Clean up old ones
  this.duplicateSubscriptions.forEach(sub => sub.unsubscribe());
  this.duplicateSubscriptions = [];

  this.cards.forEach((formGroup, i) => {
    const control = formGroup.get('search');
    if (!control) return;

    const sub = control.valueChanges.subscribe(() => {
      this.reapplyDuplicateValidators(); // ✅ live re-check all
    });

    this.duplicateSubscriptions.push(sub);
  });
}