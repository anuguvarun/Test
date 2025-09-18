// infer element type from AccountPositionsResponseInfo[]
type AccountItem =
  AccountPositionsResponseInfo extends (infer T)[] ? T : never;

// infer position type from AccountItem.positions[]
type PositionItem =
  AccountItem extends { positions: (infer P)[] } ? P : never;

const makeAccountPositions = (
  positions: PositionItem[],
  acct = 'ABC123'
): AccountPositionsResponseInfo => {
  const item: AccountItem = {
    brokerageAccountNumber: acct,
    positions,
  } as AccountItem;

  return [item];
};