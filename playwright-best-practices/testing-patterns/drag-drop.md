# File Drop Zone Testing

> **When to use**: Testing file uploads through a drag-and-drop zone.

```typescript
import { test, expect } from '@playwright/test';
import path from 'path';

test('uploads file via drop zone', async ({ page }) => {
  await page.goto('/upload');

  const dropZone = page.locator('[data-testid="file-drop-zone"]');

  await expect(dropZone).toContainText('Drag files here');

  const fileInput = page.locator('input[type="file"]');

  await fileInput.setInputFiles(path.resolve(__dirname, '../fixtures/report.pdf'));

  await expect(page.getByText('report.pdf')).toBeVisible();
  await expect(page.getByText(/\d+ KB/)).toBeVisible();
});

test('simulates drag-over visual feedback', async ({ page }) => {
  await page.goto('/upload');

  const dropZone = page.locator('[data-testid="file-drop-zone"]');

  const dataTransfer = await page.evaluateHandle(() => {
    const transfer = new DataTransfer();
    transfer.items.add(new File(['content'], 'report.pdf', { type: 'application/pdf' }));
    return transfer;
  });
  await dropZone.dispatchEvent('dragenter', { dataTransfer });

  const dragHint = dropZone.getByText(/drop.*here|release.*upload/i);
  await expect(dragHint).toBeVisible();

  await dropZone.dispatchEvent('dragleave');

  await expect(dragHint).not.toBeVisible();
});

test('rejects invalid file types', async ({ page }) => {
  await page.goto('/upload');

  const fileInput = page.locator('input[type="file"]');

  await fileInput.setInputFiles({
    name: 'script.exe',
    mimeType: 'application/x-msdownload',
    buffer: Buffer.from('fake-content'),
  });

  await expect(page.getByRole('alert')).toContainText(/not allowed|invalid file type/i);
  await expect(page.getByText('script.exe')).not.toBeVisible();
});
```
