// duplicate-search.validator.ts
import { AbstractControl, FormGroup, ValidationErrors, ValidatorFn } from '@angular/forms';
import { AccountPositionsResponseInfo } from '...';

/** Collect all positions without using Array.prototype.flatMap */
function collectAllPositions(resp?: AccountPositionsResponseInfo | null) {
  const accounts = resp?.accountPositions ?? [];
  const out: any[] = [];
  for (let i = 0; i < accounts.length; i++) {
    const posArr = accounts[i]?.positions ?? [];
    for (let j = 0; j < posArr.length; j++) out.push(posArr[j]);
  }
  return out;
}

/** Resolve whatever the user typed/selected in `search` to a CUSIP from the response. */
function resolveCusipFromResponse(
  search: unknown,
  resp?: AccountPositionsResponseInfo | null
): string | null {
  if (typeof search !== 'string') return null;
  const q = search.trim();
  if (!q) return null;

  const Q = q.toUpperCase();
  const positions = collectAllPositions(resp);

  // Match by cusip, then symbol, then description (adjust as needed)
  let match =
    positions.find((p: any) => p?.cusip && String(p.cusip).toUpperCase() === Q) ||
    positions.find((p: any) => p?.symbol && String(p.symbol).toUpperCase() === Q) ||
    positions.find((p: any) => p?.description && String(p.description).toUpperCase() === Q);

  return match?.cusip ?? null;
}

/** Validator: flags when multiple cards resolve to the same CUSIP from the response */
export function duplicateCusipAcrossCardsValidator(
  getCards: () => FormGroup[],
  getPositionsResponse: () => AccountPositionsResponseInfo | null
): ValidatorFn {
  return (control: AbstractControl): ValidationErrors | null => {
    const cards = getCards?.() ?? [];
    const group = control.parent as FormGroup | null;
    if (!cards.length || !group) return null;

    const resp = getPositionsResponse?.() ?? null;

    const currentCusip = resolveCusipFromResponse(group.get('search')?.value, resp);
    if (!currentCusip) return null;

    let sameCusipCount = 0;
    for (let i = 0; i < cards.length; i++) {
      const card = cards[i];
      const cusip = resolveCusipFromResponse(card.get('search')?.value, resp);
      if (cusip && cusip.toUpperCase() === currentCusip.toUpperCase()) {
        sameCusipCount++;
      }
    }

    return sameCusipCount > 1 ? { duplicateCusip: true } : null;
  };
}