import { FormControl, FormGroup } from '@angular/forms';
import { duplicateSearchValidator } from './duplicateSearchValidator';

describe('duplicateSearchValidator (ticker symbols, extended coverage)', () => {
  const createGroup = (ticker: any): FormGroup =>
    new FormGroup({
      search: new FormControl(ticker)
    });

  it('should return null if ticker is undefined', () => {
    const control = new FormControl(undefined);
    const result = duplicateSearchValidator(0, [createGroup('AAPL')])(control);
    expect(result).toBeNull();
  });

  it('should return null if ticker is null', () => {
    const control = new FormControl(null);
    const result = duplicateSearchValidator(0, [createGroup('AAPL')])(control);
    expect(result).toBeNull();
  });

  it('should return null if ticker is empty string', () => {
    const control = new FormControl('');
    const result = duplicateSearchValidator(0, [createGroup('MSFT')])(control);
    expect(result).toBeNull();
  });

  it('should return null if ticker is only whitespace', () => {
    const control = new FormControl('     ');
    const result = duplicateSearchValidator(0, [createGroup('TSLA')])(control);
    expect(result).toBeNull();
  });

  it('should detect duplicate tickers in a different index', () => {
    const cards = [createGroup('TSLA'), createGroup('GOOGL')];
    const control = new FormControl('TSLA');
    const result = duplicateSearchValidator(1, cards)(control);
    expect(result).toEqual({ duplicate: true });
  });

  it('should ignore same index match', () => {
    const cards = [createGroup('AAPL'), createGroup('GOOGL'), createGroup('MSFT')];
    const control = new FormControl('GOOGL');
    const result = duplicateSearchValidator(1, cards)(control); // own index
    expect(result).toBeNull();
  });

  it('should treat lowercase and uppercase tickers as equal (duplicate)', () => {
    const cards = [createGroup('amzn')];
    const control = new FormControl('AMZN');
    const result = duplicateSearchValidator(1, cards)(control);
    expect(result).toEqual({ duplicate: true });
  });

  it('should not return duplicate for similar but different tickers', () => {
    const cards = [createGroup('AMZ')];
    const control = new FormControl('AMZN');
    const result = duplicateSearchValidator(1, cards)(control);
    expect(result).toBeNull();
  });

  it('should trim and match duplicate tickers', () => {
    const cards = [createGroup('  nflx   ')];
    const control = new FormControl('NFLX');
    const result = duplicateSearchValidator(1, cards)(control);
    expect(result).toEqual({ duplicate: true });
  });
});