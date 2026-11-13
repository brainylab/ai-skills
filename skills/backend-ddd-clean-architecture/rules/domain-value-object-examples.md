# Domain: value object models

## Model with `create()`

The model demonstrates construction from properties and a getter that removes trailing slashes from the base URL and encodes the file name. The `files` segment is illustrative; use the path the resource requires.

```ts
import { ValueObject } from "@core/entities/value-object";

interface FileUrlProps {
  baseUrl: string;
  filename: string;
}

export class FileUrl extends ValueObject<FileUrlProps> {
  get value(): string {
    const baseUrl = this.props.baseUrl.replace(/\/+$/, "");
    const filename = encodeURIComponent(this.props.filename);

    return `${baseUrl}/files/${filename}`;
  }

  static create(props: FileUrlProps) {
    return new FileUrl(props);
  }
}
```

## Model with named factories

In this model, the input parameters do not include the directory: each factory defines the context. The factory names and the directories are illustrative and must represent the contexts the business needs.

```ts
import { ValueObject } from "@core/entities/value-object";

interface ResourceUrlParams {
  baseUrl: string;
  filename: string;
}

interface ResourceUrlProps extends ResourceUrlParams {
  directory: "images" | "attachments";
}

export class ResourceUrl extends ValueObject<ResourceUrlProps> {
  get value(): string {
    const baseUrl = this.props.baseUrl.replace(/\/+$/, "");
    const filename = encodeURIComponent(this.props.filename);

    return `${baseUrl}/${this.props.directory}/${filename}`;
  }

  static forImage(params: ResourceUrlParams) {
    return new ResourceUrl({ ...params, directory: "images" });
  }

  static forAttachment(params: ResourceUrlParams) {
    return new ResourceUrl({ ...params, directory: "attachments" });
  }
}
```
