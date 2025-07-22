describe('getErrorConfig', () => {
  let component: any;

  beforeEach(() => {
    component = {
      isFi: false,
      search: {
        value: null, // Mock of search input value
      },
      searchOptions: [],
      tradeType: {
        value: null,
      },
      tradeTypeEnum: {
        MF: 'MF',
        ETF: 'ETF',
      },
    };
  });

  it('should return required error for missing CUSIP when isFi is true', () => {
    component.isFi = true;
    const result = component.getErrorConfig();
    expect(result.required.msg).toBe('Please enter CUSIP.');
  });

  it('should return required error for missing symbol when isFi is false', () => {
    component.isFi = false;
    const result = component.getErrorConfig();
    expect(result.required.msg).toBe('Please enter symbol.');
  });

  it('should return 9 alphanumeric characters error for FI search with invalid input', () => {
    component.isFi = true;
    component.search.value = '123';
    const result = component.getErrorConfig();
    expect(result.searchPattern.msg).toBe('Please enter 9 alphanumeric characters.');
  });

  it('should return 2 to 5 alphanumeric characters error for non-FI search with invalid input', () => {
    component.isFi = false;
    component.search.value = '1';
    const result = component.getErrorConfig();
    expect(result.searchPattern.msg).toBe('Please enter from 2 to 5 alphanumeric characters.');
  });

  it('should return security not found error when search value is invalid', () => {
    component.search.value = 'INVALID';
    const result = component.getErrorConfig();
    expect(result.securityResponse.msg).toBe('Security not found.');
  });

  it('should return valid Mutual Fund symbol error for MF trade type', () => {
    component.search.value = 'INVALID';
    component.searchOptions = ['Option1', 'Option2'];
    component.tradeType.value = 'MF';
    const result = component.getErrorConfig();
    expect(result.securitySearchError.msg).toBe('Please enter valid Mutual Fund symbol.');
  });

  it('should return valid ETF symbol error for ETF trade type', () => {
    component.search.value = 'INVALID';
    component.searchOptions = ['Option1', 'Option2'];
    component.tradeType.value = 'ETF';
    const result = component.getErrorConfig();
    expect(result.securitySearchError.msg).toBe('Please enter valid ETF symbol.');
  });

  it('should return security not found error for unsupported trade types', () => {
    component.search.value = 'INVALID';
    component.searchOptions = [];
    component.tradeType.value = 'OTHER';
    const result = component.getErrorConfig();
    expect(result.securitySearchError.msg).toBe('Security not found.');
  });

  it('should return duplicate entry error for FI CUSIP', () => {
    component.isFi = true;
    const result = component.getErrorConfig();
    expect(result.duplicate.msg).toBe('Duplicate entry, choose a different CUSIP.');
  });

  it('should return duplicate entry error for non-FI symbol', () => {
    component.isFi = false;
    const result = component.getErrorConfig();
    expect(result.duplicate.msg).toBe('Duplicate entry, choose a different symbol.');
  });
});