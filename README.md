// parent.component.spec.ts
import { AccountHeldSecuritiesComponent } from '../account-held-securities/account-held-securities.component';
import { ParentComponent } from './parent.component'; // <-- the component that has checkHeldSecurities
// If you have enums, import them; otherwise we only assert presence where needed
// import { ModalType, ButtonType } from 'your-modal-lib';

describe('ParentComponent.checkHeldSecurities', () => {
  let component: ParentComponent;

  // very light mock of the modal service the method uses
  const modalService = {
    open: jest.fn(),
  };

  // Build a realistic modalRef shape the lib usually returns
  const makeModalRef = () => ({ componentInstance: {} as any });

  beforeEach(() => {
    jest.resetAllMocks();
    component = new ParentComponent(modalService as any);
  });

  it('opens the modal with the expected config', () => {
    const modalRef = makeModalRef();
    modalService.open.mockReturnValue(modalRef);

    component.accountPositions = { accountPositions: [] } as any;
    const result = component.checkHeldSecurities();

    expect(modalService.open).toHaveBeenCalledTimes(1);
    const cfg = modalService.open.mock.calls[0][0];

    // core config checks
    expect(cfg).toEqual(
      expect.objectContaining({
        windowId: 'heldSecuritiesModal',
        noDismiss: false,
        title: 'Held securities',
        component: AccountHeldSecuritiesComponent,
        size: 'lg',
        analyticsConfig: { name: 'DF Held securities modal' },
      })
    );

    // type / buttonType could be enums; just assert they exist
    expect(cfg.type).toBeDefined();

    // button wiring
    expect(cfg.buttons).toHaveLength(1);
    expect(cfg.buttons[0]).toEqual(
      expect.objectContaining({
        text: 'Done',
        buttonId: 'confirm-button',
        action: 'dismiss',
      })
    );
    // button type might be an enum; ensure it’s present
    expect(cfg.buttons[0].type).toBeDefined();

    // returns the ref we got from the service
    expect(result).toBe(modalRef);
  });

  it('passes accountPositions down to the modal component instance', () => {
    const modalRef = makeModalRef();
    modalService.open.mockReturnValue(modalRef);

    const positionsPayload = [{ marketValue: 1, holdingPct: 0.5 }];
    component.accountPositions = { accountPositions: positionsPayload } as any;

    component.checkHeldSecurities();

    expect(modalRef.componentInstance.accountPositions).toBe(positionsPayload);
  });

  it('sets undefined when accountPositions is missing (safe optional chaining)', () => {
    const modalRef = makeModalRef();
    modalService.open.mockReturnValue(modalRef);

    component.accountPositions = undefined as any;

    component.checkHeldSecurities();

    expect(modalRef.componentInstance.accountPositions).toBeUndefined();
  });

  it('is safe if modal service returns undefined and returns that value', () => {
    modalService.open.mockReturnValue(undefined);

    expect(() => component.checkHeldSecurities()).not.toThrow();
    expect(component.checkHeldSecurities()).toBeUndefined();
  });
});