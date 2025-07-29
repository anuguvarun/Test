it('should return null if index of group is -1 but cards is defined', () => {
  const validGroup = new FormGroup({
    search: new FormControl('apple'),
    tradeType: new FormControl('buy')
  });

  const cards = []; // defined, but empty — so indexOf will return -1
  const validator = duplicateSearchValidator(() => cards);
  expect(validator(validGroup.get('search')!)).toBeNull();
});

it('should return null if search is only whitespace', () => {
  const group = new FormGroup({
    search: new FormControl('    '), // whitespace only
    tradeType: new FormControl('buy')
  });

  const cards = [group];
  const validator = duplicateSearchValidator(() => cards);
  expect(validator(group.get('search')!)).toBeNull();
});

it('should return null if tradeType value is undefined', () => {
  const group = new FormGroup({
    search: new FormControl('apple'),
    tradeType: new FormControl(undefined)
  });

  const cards = [group];
  const validator = duplicateSearchValidator(() => cards);
  expect(validator(group.get('search')!)).toBeNull();
});