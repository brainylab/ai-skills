# Domínio: modelos de value object

## Modelo com `create()`

O modelo demonstra a construção por propriedades e um getter que remove barras finais da URL base e codifica o nome do arquivo. O segmento `files` é ilustrativo; utilize o caminho necessário ao recurso.

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

## Modelo com fábricas nomeadas

Neste modelo, os parâmetros de entrada não incluem o diretório: cada fábrica define o contexto. Os nomes das fábricas e os diretórios são ilustrativos e devem representar os contextos necessários ao negócio.

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
