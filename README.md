l// step-card.component.spec.ts
import { Validators } from '@angular/forms';
import { StepCardComponent } from './step-card.component';

// Lightweight stubs for constructor deps
const mockPresenter = {} as any;
const mockCdr = { markForCheck: jest.fn() } as any;
const mockDocument = {} as unknown as Document;
const mockModalService = { open: jest.fn(), close: jest.fn() } as any;

describe('StepCardComponent (unit)', () => {
  let component: StepCardComponent;

  beforeEach(() => {
    component = new StepCardComponent(
      mockPresenter,
      mockCdr,
      mockDocument,
      mockModalService
    );

    // Stub out the controls the component uses in setupValidators()
    component.tradeType = { setValidators: jest.fn(), value: 'BUY' } as any;
    component.actionToggle = { setValidators: jest.fn() } as any;
    component.shareToggle = { setValidators: jest.fn() } as any;
    component.search = { setValidators: jest.fn() } as any;

    // Properties referenced by the validator factories
    (component as any).securityPriceData = { some: 'data' };
    (component as any).searchSent = false;
  });

  it('ngOnInit calls updateValidators', () => {
    const spy = jest.spyOn(component as any, 'updateValidators');
    component.ngOnInit();
    expect(spy).toHaveBeenCalled();
  });

  it('createDisplayLabels maps codes to {value, displayValue}', () => {
    const codes = { A: 'Alpha', B: 'Beta' };
    const result = component.createDisplayLabels(codes as any);

    expect(result).toEqual([
      { value: 'A', displayValue: 'Alpha' },
      { value: 'B', displayValue: 'Beta' },
    ]);
  });

  it('setupValidators sets validators for all controls', () => {
    component.setupValidators();

    // tradeType / actionToggle / shareToggle each get [Validators.required]
    expect(component.tradeType.setValidators).toHaveBeenCalledWith([Validators.required]);
    expect(component.actionToggle.setValidators).toHaveBeenCalledWith([Validators.required]);
    expect(component.shareToggle.setValidators).toHaveBeenCalledWith([Validators.required]);

    // search gets [required, searchValidator(...), securityResponseValidator(...)]
    expect((component.search.setValidators as jest.Mock).mock.calls.length).toBe(1);

    const validatorsArray = (component.search.setValidators as jest.Mock).mock.calls[0][0];
    expect(Array.isArray(validatorsArray)).toBe(true);
    expect(validatorsArray[0]).toBe(Validators.required);
    // the next two items are validator functions produced by the factories
    expect(typeof validatorsArray[1]).toBe('function');
    expect(typeof validatorsArray[2]).toBe('function');
  });
});