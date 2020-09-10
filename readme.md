## Endereços de imagens
1. Alterar a environment variable APP_API_URL para o ip da máquina.

> posso rodar o comando adb reverse tcp:3333 tcp:3333
> invalidar o cache para checar o *avatar_url*

## Criando páginas e rotas
1. Criar CreateAppointment/AppointmentCreated/Profile page.

## Header do Dashboard
1. Fazer a criação do header e estilizar.

## Buscando providers da API
1. Conectar com a API para listar os providers.
```typescript
import React, { useEffect, useState } from 'react';
import api from '../../services/api';

export interface Provider {
  id: string;
  name: string;
  avatar_url: string;
}

const Dashboard: React.FC = () => {
  const [providers, setProviders] = useState<Provider[]>([]);

  useEffect(() => {
    api.get('providers').then(response => setProviders(response.data));
  }, []);


  return (
    ...
  );
}
```

2. Fazer a estilização da FlatList
```typescript
import styled from 'styled-components/native';
import { FlatList } from 'react-native';

import { Provider } from './index';

export const ProvidersList = styled(Flatlist as new () => FlatList<Provider>)``;
```

3. Importar ProvidersList no index e exibir os nomes dos providers em tela.
```typescript
import React, { useEffect, useState } from 'react';
import api from '../../services/api';
import { ..., ProvidersList } from './styles';

export interface Provider {
  id: string;
  name: string;
  avatar_url: string;
}

const Dashboard: React.FC = () => {
  const [providers, setProviders] = useState<Provider[]>([]);

  useEffect(() => {
    api.get('providers').then(response => setProviders(response.data));
  }, []);


  return (
    ...
    <ProvidersList
      data={providers}
      keyExtractor={provider => provider.id}
      renderItem={({ item }) => (
        <UserName>{item.name}</UserName>
      )}
     />
  );
}
```
