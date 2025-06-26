onToggleUnitType(unitType: UnitType): void {
  this.card.get('shareToggle')?.setValue(unitType);

  const inputField = unitType === UnitType.Shares ? 'quantity' : 'amount';
  const control = this.card.get(inputField);

  // ✅ This forces validation AND allows UI to show errors immediately
  control?.markAsTouched();
  control?.updateValueAndValidity();
}