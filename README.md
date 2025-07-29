it('should return null if cards is null', () => {
  const validator = duplicateSearchValidator(() => null as any);
  const control = new FormControl('test');
  expect(validator(control)).toBeNull();
});

it('should return null if tradeType control is missing', () => {
  const group = new FormGroup({
    search: new FormControl('apple'),
  });
  const cards = [group];
  const validator = duplicateSearchValidator(() => cards);
  expect(validator(group.get('search')!)).toBeNull();
});

it('should return null if tradeType control value is null', () => {
  const group = new FormGroup({
    search: new FormControl('apple'),
    tradeType: new FormControl(null),
  });
  const cards = [group];
  const validator = duplicateSearchValidator(() => cards);
  expect(validator(group.get('search')!)).toBeNull();
});