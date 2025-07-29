import { FormControl, FormGroup } from '@angular/forms';
import { duplicateSearchValidator } from './your-validator-file'; // Update path accordingly

// Helper to create a FormGroup with FormControls
const createGroup = (search: any, tradeType: any = 'BUY') =>
  new FormGroup({
    search: new FormControl(search),
    tradeType: new FormControl(tradeType)
  });

describe('duplicateSearchValidator (FormGroup)', () => {
  it('should return null if search is undefined', () => {
    const group = createGroup(undefined);
    const control = group.get('search')!;
    const result = duplicateSearchValidator(() => [group])(control);
    expect(result).toBeNull();
  });

  it('should return null if search is null', () => {
    const group = createGroup(null);
    const control = group.get('search')!;
    const result = duplicateSearchValidator(() => [group])(control);
    expect(result).toBeNull();
  });

  it('should return null if search is empty string', () => {
    const group = createGroup('');
    const control = group.get('search')!;
    const result = duplicateSearchValidator(() => [group])(control);
    expect(result).toBeNull();
  });

  it('should return null if search is only whitespace', () => {
    const group = createGroup('   ');
    const control = group.get('search')!;
    const result = duplicateSearchValidator(() => [group])(control);
    expect(result).toBeNull();
  });

  it('should detect duplicate search + tradeType in a different index', () => {
    const group1 = createGroup('TSLA', 'BUY');
    const group2 = createGroup('GOOGL', 'BUY');
    const group3 = createGroup('TSLA', 'BUY');
    const cards = [group1, group2, group3];

    const control = group3.get('search')!;
    const result = duplicateSearchValidator(() => cards)(control);
    expect(result).toEqual({ duplicate: true });
  });

  it('should ignore same index match', () => {
    const group1 = createGroup('AAPL', 'BUY');
    const group2 = createGroup('GOOGL', 'BUY');
    const group3 = createGroup('MSFT', 'BUY');
    const cards = [group1, group2, group3];

    const control = group2.get('search')!;
    const result = duplicateSearchValidator(() => cards)(control);
    expect(result).toBeNull();
  });

  it('should treat lowercase and uppercase search as equal if tradeType matches', () => {
    const group1 = createGroup('amzn', 'SELL');
    const group2 = createGroup('AMZN', 'SELL');
    const cards = [group1, group2];

    const control = group2.get('search')!;
    const result = duplicateSearchValidator(() => cards)(control);
    expect(result).toEqual({ duplicate: true });
  });

  it('should NOT return duplicate for same search with different tradeType', () => {
    const group1 = createGroup('NFLX', 'BUY');
    const group2 = createGroup('NFLX', 'SELL');
    const cards = [group1, group2];

    const control = group2.get('search')!;
    const result = duplicateSearchValidator(() => cards)(control);
    expect(result).toBeNull(); // No duplicate since tradeTypes differ
  });

  it('should trim and match duplicate search + tradeType', () => {
    const group1 = createGroup(' nflx ', 'BUY');
    const group2 = createGroup('NFLX', 'BUY');
    const cards = [group1, group2];

    const control = group2.get('search')!;
    const result = duplicateSearchValidator(() => cards)(control);
    expect(result).toEqual({ duplicate: true });
  });
});