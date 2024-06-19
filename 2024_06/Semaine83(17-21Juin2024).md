**17 Juin**
- [x] Résolution du problème Error response from daemon: denied
    - [x] Consultation de (https://forums.docker.com/t/docker-error-response-from-daemon-pull-access-denied-for-docker-poject-repository-does-not-exist-or-may-require-docker-login-denied-requested-access-to-the-resource-is-denied/126237/7)
    - [x] Essais de modifications sur le docker compose
        - [x] En fait je lançais la commande de prod, mais même résultat en dev
        - [x] C'est les parties mqtt du docker compose qui posent problème ; je n'ai pas accès ??
    - [x] Jacques m'a ajouté aux droits des repos github
- [x] Fix du testing sur la branche esl-32
- [ ] Ecriture des tests de l'esl service backend


**19 Juin**
- [x] Écriture des tests pour le fichier pictures service ts : 
```
import { Test, TestingModule } from '@nestjs/testing';
import { PicturesService } from './pictures.service';
import { HttpModule, HttpService } from '@nestjs/axios';
import { of, throwError } from 'rxjs';
import { AxiosResponse } from 'axios';
import { CreatePictureDto } from './dto/create-picture.dto';
import { UpdatePictureDto } from './dto/update-picture.dto';

describe('PicturesService', () => {
  let service: PicturesService;
  let httpService: HttpService;

  beforeEach(async () => {
    const module: TestingModule = await Test.createTestingModule({
      imports: [HttpModule],
      providers: [PicturesService],
    }).compile();

    service = module.get<PicturesService>(PicturesService);
    httpService = module.get<HttpService>(HttpService);
  });

  it('should be defined', () => {
    expect(service).toBeDefined();
  });

  describe('create', () => {
    it('should return a create message', () => {
      const createPictureDto: CreatePictureDto = { /* fill with appropriate properties */ };
      expect(service.create(createPictureDto)).toBe(`This action adds a new picture ${createPictureDto}`);
    });
  });

  describe('findAll', () => {
    it('should return all pictures', () => {
      expect(service.findAll()).toBe('This action returns all pictures');
    });
  });

  describe('findOne', () => {
    it('should return a single picture', () => {
      const id = 1;
      expect(service.findOne(id)).toBe(`This action returns a #${id} picture`);
    });
  });

  describe('update', () => {
    it('should send an update request and handle response', done => {
      const id_esl = '1';
      const updatePictureDto: UpdatePictureDto = { picture: 'new-image' };

      const response: AxiosResponse = {
        data: { success: true },
        status: 200,
        statusText: 'OK',
        headers: {},
        config: {},
      } as AxiosResponse;

      jest.spyOn(httpService, 'post').mockReturnValue(of(response));

      service.update(id_esl, updatePictureDto).subscribe(result => {
        expect(result).toEqual(response.data);
        done();
      });
    });

    it('should handle errors during update request', done => {
      const id_esl = '1';
      const updatePictureDto: UpdatePictureDto = { picture: 'new-image' };

      const errorResponse = new Error('Request failed');
      jest.spyOn(httpService, 'post').mockReturnValue(throwError(() => errorResponse));

      service.update(id_esl, updatePictureDto).subscribe(result => {
        expect(result).toEqual({ success: false, message: 'Failed to send image', error: 'Request failed' });
        done();
      });
    });
  });

  describe('remove', () => {
    it('should return a remove message', () => {
      const id = 1;
      expect(service.remove(id)).toBe(`This action removes a #${id} picture`);
    });
  });
});
```
ce code fait fail un test : 
```
● PicturesService › update › should send an update request and handle response

thrown: "Exceeded timeout of 5000 ms for a test.
Use jest.setTimeout(newTimeout) to increase the timeout value, if this is a long-running test."

    46 |
    47 |   describe('update', () => {
> 48 |     it('should send an update request and handle response', done => {
    |     ^
49 |       const id_esl = '1';
50 |       const updatePictureDto: UpdatePictureDto = { picture: 'new-image' };
51 |

at pictures/pictures.service.spec.ts:48:5
at pictures/pictures.service.spec.ts:47:3
at Object.<anonymous> (pictures/pictures.service.spec.ts:9:1)
```
- [ ] Résolution du fail