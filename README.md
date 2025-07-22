import { YourComponent } from './your-component-file';

describe('YourComponent errorConfig getter', () => {
  let component: YourComponent;

  beforeEach(() => {
    component = new YourComponent();
  });

  it('should return correct required message for isFi=true', () => {
    component.isFi = true;
    expect(component.errorConfig.required.msg).toBe('Please enter CUSIP.');
  });

  it('should return correct required message for isFi=false', () => {
    component.isFi = false;
    expect(component.errorConfig.required.msg).toBe('Please enter symbol.');
  });

  it('should return correct searchPattern for CUSIP', () => {
    component.search.value = '123456789';
    component.isFi = true;
    expect(component.errorConfig.searchPattern.msg).toBe('Please enter 9 alphanumeric characters.');
  });

  it('should return correct searchPattern for symbol', () => {
    component.search.value = 'AAPL';
    component.isFi = false;
    expect(component.errorConfig.searchPattern.msg).toBe('Please enter from 2 to 5 alphanumeric characters.');
  });

  it('should return securityResponse for valid input', () => {
    component.search.value = 'AAPL';
    expect(component.errorConfig.securityResponse.msg).toBe('Security not found.');
  });

  it('should return securitySearchError for MF', () => {
    component.search.value = 'FOO';
    component.tradeType.value = 'MF';
    component.searchOptions = [];
    expect(component.errorConfig.securitySearchError.msg).toBe('Please enter valid Mutual Fund symbol.');
  });

  it('should return securitySearchError for ETF', () => {
    component.search.value = 'BAR';
    component.tradeType.value = 'ETF';
    component.searchOptions = [];
    expect(component.errorConfig.securitySearchError.msg).toBe('Please enter valid ETF symbol.');
  });

  it('should return fallback securitySearchError', () => {
    component.search.value = 'XXX';
    component.tradeType.value = 'OTHER';
    component.searchOptions = [];
    expect(component.errorConfig.securitySearchError.msg).toBe('Security not found.');
  });

  it('should return duplicate CUSIP message if isFi=true', () => {
    component.isFi = true;
    expect(component.errorConfig.duplicate.msg).toBe('Duplicate entry, choose a different CUSIP.');
  });

  it('should return duplicate symbol message if isFi=false', () => {
    component.isFi = false;
    expect(component.errorConfig.duplicate.msg).toBe('Duplicate entry, choose a different symbol.');
  });
});