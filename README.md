it('should return null when no other tickers match (non-duplicate path)', () => {
  const cards = [createGroup('AAPL'), createGroup('GOOGL')];
  const control = new FormControl('TSLA');
  const result = duplicateSearchValidator(2, cards)(control);
  expect(result).toBeNull(); // hits false branch of line 14
});