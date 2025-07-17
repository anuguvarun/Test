it('should explicitly hit the false branch of isDuplicate and return null', () => {
  const cards = [createGroup('AAPL'), createGroup('GOOGL'), createGroup('MSFT')];
  const control = new FormControl('TSLA'); // not a duplicate
  const result = duplicateSearchValidator(1, cards)(control); // any index not 0
  expect(result).toBeNull(); // hits the "false" path of ternary
});