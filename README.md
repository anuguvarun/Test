export function duplicateSearchValidator(index: number, cards: FormGroup[]): ValidatorFn {
  return (control: AbstractControl): ValidationErrors | null => {
    const currentValue = control.value?.trim()?.toLowerCase();
    if (!currentValue) return null;

    let foundAt = -1;

    for (let i = 0; i < cards.length; i++) {
      const val = cards[i].get('search')?.value?.trim()?.toLowerCase();
      if (val === currentValue) {
        if (foundAt === -1) {
          foundAt = i; // first time seen
        } else if (i === index) {
          // this is a later duplicate
          return { duplicate: true };
        }
      }
    }

    return null; // either first occurrence or no match
  };
}