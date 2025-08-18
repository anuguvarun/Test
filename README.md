function resolveCusipFromResponse(
  search: unknown,
  resp?: AccountPositionsResponseInfo | null
): string | null {
  const q = typeof search === 'string' ? search.trim() : '';
  if (!q) return null;

  const Q = q.toUpperCase();

  const positions = resp?.accountPositions?.flatMap(a => a.positions ?? []) ?? [];

  let pos =
    positions.find(p => p?.cusip?.toUpperCase() === Q) ??
    positions.find(p => p?.symbol?.toUpperCase() === Q) ??
    positions.find(p => p?.description?.toUpperCase() === Q);

  return pos?.cusip ?? null;
}