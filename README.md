this.cards.forEach(group => {
  group.get('cardValue')?.valueChanges.subscribe(() => {
    this.checkForDuplicateCardValues();
  });
});

checkForDuplicateCardValues(): void {
  const valueMap = new Map<string, number[]>();

  // Step 1: Build a map of value to indices
  this.cards.forEach((group, index) => {
    const value = group.get('cardValue')?.value?.trim()?.toLowerCase();
    if (value) {
      if (!valueMap.has(value)) {
        valueMap.set(value, []);
      }
      valueMap.get(value)!.push(index);
    }
  });

  // Step 2: Clear previous errors
  this.cards.forEach(group => {
    const control = group.get('cardValue');
    if (control?.errors) {
      const { duplicate, ...otherErrors } = control.errors;
      if (Object.keys(otherErrors).length > 0) {
        control.setErrors(otherErrors);
      } else {
        control.setErrors(null);
      }
    }
  });

  // Step 3: Apply duplicate errors
  valueMap.forEach(indices => {
    if (indices.length > 1) {
      indices.forEach(i => {
        const control = this.cards[i].get('cardValue');
        const currentErrors = control?.errors || {};
        control?.setErrors({ ...currentErrors, duplicate: true });
      });
    }
  });
}