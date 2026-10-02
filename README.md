# -bugweg-test
test-change
rely on bugweg
// Test file for bugweg: contains deliberate bugs. Do not use in production.

// Secrets Detection: AWS's documented example key (not a real credential)
const AWS_ACCESS_KEY_ID = "AKIAIOSFODNN7EXAMPLE";

interface Order {
  id: string;
  total: number;
  customer?: { email: string };
}

// Bug 1: null dereference. customer is optional but used directly
export function receiptEmail(order: Order): string {
  return order.customer.email.toLowerCase();
}

// Bug 2: off-by-one. Skips the last refund line
export function refundTotal(lines: number[]): number {
  let sum = 0;
  for (let i = 0; i < lines.length - 1; i++) {
    sum += lines[i];
  }
  return sum;
}

// Bug 3: SQL injection. User input concatenated into a query
export function findOrderQuery(orderId: string): string {
  return "SELECT * FROM orders WHERE id = '" + orderId + "'";
}

// Bug 4: money as floats, and the error is swallowed
export function applyRefund(order: Order, amount: number): number {
  try {
    if (amount > order.total) throw new Error("refund exceeds total");
    return order.total - amount;
  } catch (e) {
    return order.total;
  }
}

// Bug 5: the return type promises a number, but it can return undefined
export function discountRate(code: string): number {
  const rates: Record<string, number> = { SAVE10: 0.1, SAVE20: 0.2 };
  return rates[code];
}
