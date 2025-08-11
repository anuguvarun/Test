import { FormGroup, FormControl, Validators } from '@angular/forms';
import { QueryList, EventEmitter } from '@angular/core';
import { YourComponent } from './your-component-file'; // adjust import

const makeQueryList = <T,>(arr: T[]): QueryList<T> => {
  const ql = new QueryList<T>();
  (ql as any).reset(arr);
  (ql as any)._notifyOnChanges();
  return ql;
};

describe('onNextStep', () => {
  let component: YourComponent;
  let routerStub: any;
  let presenterStub: any;

  beforeEach(() => {
    routerStub = { navigateByUrl: jest.fn() };
    presenterStub = { checkAccountNumberValidity: jest.fn() };

    component = new YourComponent(presenterStub, routerStub); // adapt to your constructor
    component.stepDataSubmitted = new EventEmitter<any>();

    // mock window scrolling
    (global as any).window.scrollY = 50;
    (global as any).window.scrollTo = jest.fn();
    jest.useFakeTimers();
  });

  const mkCard = (valid: boolean) => {
    const fg = new FormGroup({ ctrl: new FormControl(valid ? 'x' : '') });
    Object.defineProperties(fg, { valid: { get: () => valid } });
    (fg as any).markAllAsTouched = jest.fn();
    (fg as any).markAsPristine = jest.fn();
    (fg as any).updateValueAndValidity = jest.fn();
    return fg;
  };

  it('calls presenter with multiple accounts and navigates when valid', () => {
    component.accounts = [{}, {}];
    component.headerForm = new FormGroup({ brokerageAccount: new FormControl('ACC', Validators.required) });
    component.cards = [mkCard(true)];
    component.adjustedAvailableBalance = 10;

    component.onNextStep();

    expect(presenterStub.checkAccountNumberValidity).toHaveBeenCalled();
    expect(routerStub.navigateByUrl).toHaveBeenCalledWith('/invest/trade-order-confirm');
  });

  it('does not call presenter with single account', () => {
    component.accounts = [{}];
    component.headerForm = new FormGroup({ brokerageAccount: new FormControl('ACC', Validators.required) });
    component.cards = [mkCard(true)];
    component.adjustedAvailableBalance = 10;

    component.onNextStep();

    expect(presenterStub.checkAccountNumberValidity).not.toHaveBeenCalled();
  });

  it('emits event when valid but balance negative', () => {
    const emitSpy = jest.spyOn(component.stepDataSubmitted, 'emit');
    component.accounts = [{}, {}];
    component.headerForm = new FormGroup({ brokerageAccount: new FormControl('ACC', Validators.required) });
    component.cards = [mkCard(true)];
    component.adjustedAvailableBalance = -5;

    component.onNextStep();

    expect(routerStub.navigateByUrl).not.toHaveBeenCalled();
    expect(emitSpy).toHaveBeenCalled();
  });

  it('scrolls to first invalid card element', () => {
    component.accounts = [{}, {}];
    component.headerForm = new FormGroup({ brokerageAccount: new FormControl('ACC', Validators.required) });
    component.cards = [mkCard(false), mkCard(true)];
    const native = { 
      getBoundingClientRect: () => ({ top: 200 }),
      scrollIntoView: jest.fn() 
    };
    component.stepCardElements = makeQueryList<any>([{ nativeElement: native }]);

    component.onNextStep();
    jest.runOnlyPendingTimers();

    expect(native.scrollIntoView).toHaveBeenCalled();
    expect((global as any).window.scrollTo).toHaveBeenCalled();
  });

  it('does not scroll if scroll element missing', () => {
    component.accounts = [{}, {}];
    component.headerForm = new FormGroup({ brokerageAccount: new FormControl('ACC', Validators.required) });
    component.cards = [mkCard(false)];
    component.stepCardElements = makeQueryList<any>([undefined as any]);

    component.onNextStep();
    jest.runOnlyPendingTimers();

    expect((global as any).window.scrollTo).not.toHaveBeenCalled();
  });
});