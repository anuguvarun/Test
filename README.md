// __tests__/StepCardComponent.test.ts

import StepCardComponent from '../components/StepCardComponent'; // adjust path as needed

describe('StepCardComponent - errorConfig', () => {
  const tradeTypeEnum = { MF: 'MF', ETF: 'ETF' };

  const createInstance = (overrides = {}) => {
    const component = new StepCardComponent();
    component.isFi = false;
    component.search = { value: '' };
    component.searchOptions = [];
    component.tradeType = { value: '' };
    component.tradeTypeEnum = tradeTypeEnum;

    Object.assign(component, overrides);
    return component;
  };

  it('should return CUSIP error when isFi is true', () => {
    const instance = createInstance({ isFi: true });
    expect(instance.errorConfig.required.msg).toBe('Please enter CUSIP.');
  });

  it('should return symbol error when isFi is false', () => {
    const instance = createInstance({ isFi: false });
    expect(instance.errorConfig.required.msg).toBe('Please enter symbol.');
  });

  it('should validate searchPattern for CUSIP', () => {
    const instance = createInstance({ isFi: true, search: { value: '123' } });
    expect(instance.errorConfig.searchPattern.msg).toBe('Please enter 9 alphanumeric characters.');
  });

  it('should validate searchPattern for symbol', () => {
    const instance = createInstance({ isFi: false, search: { value: 'XYZ' } });
    expect(instance.errorConfig.searchPattern.msg).toBe('Please enter from 2 to 5 alphanumeric characters.');
  });

  it('should return security not found if input is given', () => {
    const instance = createInstance({ search: { value: 'ZZZ' } });
    expect(instance.errorConfig.securityResponse.msg).toBe('Security not found.');
  });

  it('should return MF symbol error if tradeType is MF and searchOptions empty', () => {
    const instance = createInstance({
      search: { value: 'MF123' },
      searchOptions: [],
      tradeType: { value: 'MF' },
    });
    expect(instance.errorConfig.securitySearchError.msg).toBe('Please enter valid Mutual Fund symbol.');
  });

  it('should return ETF symbol error if tradeType is ETF and searchOptions empty', () => {
    const instance = createInstance({
      search: { value: 'ETF456' },
      searchOptions: [],
      tradeType: { value: 'ETF' },
    });
    expect(instance.errorConfig.securitySearchError.msg).toBe('Please enter valid ETF symbol.');
  });

  it('should return default security not found error if tradeType unknown', () => {
    const instance = createInstance({
      search: { value: 'XYZ' },
      searchOptions: [],
      tradeType: { value: 'OTHER' },
    });
    expect(instance.errorConfig.securitySearchError.msg).toBe('Security not found.');
  });

  it('should return duplicate CUSIP message if isFi is true', () => {
    const instance = createInstance({ isFi: true });
    expect(instance.errorConfig.duplicate.msg).toBe('Duplicate entry, choose a different CUSIP.');
  });

  it('should return duplicate symbol message if isFi is false', () => {
    const instance = createInstance({ isFi: false });
    expect(instance.errorConfig.duplicate.msg).toBe('Duplicate entry, choose a different symbol.');
  });
});