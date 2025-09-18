// infer element type from the accountPositions array inside AccountPositionsResponseInfo
type AccountItem =
  AccountPositionsResponseInfo extends { accountPositions: (infer T)[] } ? T : never;

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
  };

  // return the exact structure the model expects (no casts)
  return { accountPositions: [item] };
};