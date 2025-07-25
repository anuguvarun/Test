import { FormControl, FormGroup } from '@angular/forms';
import { duplicateSearchValidator } from './duplicate-search.validator';

describe('duplicateSearchValidator (search + tradeType coverage)', () => {
  const createGroup = (search: any, tradeType: any = 'BUY'): FormGroup =>
    new FormGroup({
      search: new FormControl(search),
      tradeType: new FormControl(tradeType)
    });

  it('should return null if search is undefined', () => {
    const control = new FormControl(undefined);
    const result = duplicateSearchValidator(0, [createGroup('AAPL')])(control);
    expect(result).toBeNull();
  });

  it('should return null if search is null', () => {
    const control = new FormControl(null);
    const result = duplicateSearchValidator(0, [createGroup('AAPL')])(control);
    expect(result).toBeNull();
  });

  it('should return null if search is empty string', () => {
    const control = new FormControl('');
    const result = duplicateSearchValidator(0, [createGroup('MSFT')])(control);
    expect(result).toBeNull();
  });

  it('should return null if search is only whitespace', () => {
    const control = new FormControl(' ');
    const result = duplicateSearchValidator(0, [createGroup('TSLA')])(control);
    expect(result).toBeNull();
  });

  it('should detect duplicate search + tradeType in a different index', () => {
    const cards = [
      createGroup('TSLA', 'BUY'),
      createGroup('GOOGL', 'BUY'),
      createGroup('TSLA', 'BUY') // Duplicate of index 0
    ];
    const control = new FormControl('TSLA');
    const result = duplicateSearchValidator(2, cards)(control);
    expect(result).toEqual({ duplicate: true });
  });

  it('should ignore same index match', () => {
    const cards = [
      createGroup('AAPL', 'BUY'),
      createGroup('GOOGL', 'BUY'),
      createGroup('MSFT', 'BUY')
    ];
    const control = new FormControl('GOOGL');
    const result = duplicateSearchValidator(1, cards)(control);
    expect(result).toBeNull();
  });

  it('should treat lowercase and uppercase search as equal if tradeType matches', () => {
    const cards = [
      createGroup('amzn', 'SELL'),
      createGroup('AMZN', 'SELL')
    ];
    const control = new FormControl('AMZN');
    const result = duplicateSearchValidator(1, cards)(control);
    expect(result).toEqual({ duplicate: true });
  });

  it('should NOT return duplicate for same search with different tradeType', () => {
    const cards = [
      createGroup('NFLX', 'BUY'),
      createGroup('NFLX', 'SELL')
    ];
    const control = new FormControl('NFLX');
    const result = duplicateSearchValidator(1, cards)(control);
    expect(result).toBeNull(); // No duplicate since tradeTypes differ
  });

  it('should trim and match duplicate search + tradeType', () => {
    const cards = [
      createGroup(' nflx ', 'BUY'),
      createGroup('NFLX', 'BUY')
    ];
    const control = new FormControl('NFLX');
    const result = duplicateSearchValidator(1, cards)(control);
    expect(result).toEqual({ duplicate: true });
  });
});